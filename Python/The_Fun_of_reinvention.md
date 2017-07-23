# The Fun of Reinvention

Invited Keynote Talk from PyCon Israel, June 12, 2017. I cause trouble and build a framework using all sorts of new Python 3.6+ features.

이 글의 핵심은 'Python 3.6 을 **최대한** 활용' 이다!  
저자는 type checking, validation 등을 간편하게 할 수 있는 방법을 소개한다.

## 1. F-String

```python
>>> born = 1991
>>> died = 2016
>>> print(f'RIP: {born} - {died}')
RIP: 1991 - 2016

>>> f'{born}'
'1991'
>>> f"{f'Age {died - born}':*^25}"
'*********Age 25**********'
```

- F-String 내부 연산 가능
- Nested F-String 도 가능


## 2. Annotation

중간 과정은 생략하고, 최종 코드.

```python
# contract.py

class Contract:
    @classmethod
    def check(cls, value):
        pass

class Typed(Contract):
    @classmethod
    def check(cls, value):
        assert isinstance(value, cls.type), f'Expected {cls.type}'
        super().check(value)

class Integer(Typed):
    type = int

class Float(Typed):
    type = float

class String(Typed):
    type = str
    
class Positive(Contract):
    @classmethod
    def check(cls, value):
        assert value > 0, 'Must be > 0'
        super().check(value)
    
class PositiveInteger(Integer, Positive):
    pass
```

- Type Check, Validation용 클래스

<hr/>

```python
from functools import wraps
from inspect import signature

def checked(func):
    sig = signature(func)
    ann = func.__annotations__
    @wraps(func)
    def wrapper(*args, **kwargs):
        bound = sig.bind(*args, **kwargs)
        for name, val in bound.arguments.items():
            if name in ann:
                ann[name].check(val)
        return func(*args, **kwargs)
    return wrapper
```

- \_\_annotations\_\_
- inspect 모듈은 파이썬 오브젝트에 대한 정보를 가져올 수 있는 모듈

<hr/>

```python
>>> from inspect import signature
>>> signature(gcd)
<Signature (a:__main__.PositiveInteger, b:__main__.PositiveInteger)>
>>> signature(gcd).bind(1, 4)
<BoundArguments (a=1, b=4)>
>>> signature(gcd).bind(1, 4).arguments
OrderedDict([('a', 1), ('b', 4)])
```

- signature example

<hr/>

```python
@checked
def gcd(a: PositiveInteger, b: PositiveInteger):
    while b:
        a, b = b, a % b
    return a
```

- use @checked (apply type check & validation)

<hr/>

- 이와 코딩을 하면 Python 3.6에 추가된 Type Annotation을 이용해서, Type Checking, Validation 로직을 넣을 수 있게 된다.
- 굉장히 인상적이라고 생각이 들고, Python 언어가 가지는 강력함을 느낄 수 있었다.


## 3. Class Annotation

```python
# contract.py

class Contract:
    def __set__(self, instance, value):
        self.check(value)
        instance.__dict__[self.name] = value

    def __set_name__(self, owner, name):
        self.name = name

    @classmethod
    def check(cls, value):
        pass
        
...

class NonEmpty(Contract):
    @classmethod
    def check(cls, value):
        assert len(value) > 0, 'Must be nonempty'
        super().check(value)

class NonEmptyString(String, NonEmpty):
    pass
    
```

- ```__set__``` 과 ```__set_name__``` 메서드를 오버라이드
- **\_\_set\_\_** : 인스턴스에 값을 설정하는 경우 (self.a = 1, instance.a = 1)
- **\_\_set_name\_\_** : 클래스에사 값을 설정하는 경우 (t = Test())
- 그러므로 ```p.x = 'wow'``` => ```Contract.__set__(p.x, p, 'wow')```


```python
from contract import *

class Base:
    @classmethod
    def __init_subclass__(cls):
        for name, val in cls.__dict__.items():
            if callable(val):
                setattr(cls, name, checked(val))

        for name, val in cls.__annotations__.items():
            contract = val()
            contract.__set_name__(cls, name)
            setattr(cls, name, contract)

    def __init__(self, *args):
        ann = self.__annotations__
        assert len(ann) == len(args), f'Expected {len(ann)} arguments'
        for name, val in zip(ann, args):
            setattr(self, name, val)

    def __repr__(self):
        args = ', '.join(repr(getattr(self, name)) for name in self.__annotations__)
        return f'{type(self).__name__}({args})'


class Player(Base):
    name: NonEmptyString
    x: Integer
    y: Integer

    def left(self, dx: PositiveInteger):
        self.x -= dx

    def right(self, dx: PositiveInteger):
        self.x += dx
```

- ```__init_subclass__```는 ```Player.__init__```가 선언되기 전에 호출
- ```Base.__init_subclass__``` 로직을 이해하기 위해선 데코레이터 동작 방식 이해가 필요 (checked 로직을 적용하는 것)
- Class 생성과 동시에 checked 로직 진행

```python
@deco
def func:
    pass
```

위와 아래가 동일.

```python
def func:
    pass
func = deco(func)
```

```python
>>> p = Player('Player', 0, 0)
>>> p
Player('Player', 0, 0)
>>> p.left(-10)
AssertionError: Must be > 0
```

## 4. Meta Class

아마.. Python을 주로 사용하신 분들은 import * 가 거슬릴 것이다..!  
이것을 해결하기위하여 Meta Class를 사용한다.

```python
class Player(Base):
```

```python
type.__prepare__('Player', (Base,))
```

- Class 선언 동작 방식
- 메타클래스가 클래스를 선언하기 전에 ```__prepare__```를 호출
- 클래스를 선언한 뒤에는 ```__new__```를 호출

```python
>>> c = ChainMap({}, {'x': 0, 'y': 0})
>>> c['x']
0
>>> c['a'] = 42
>>> c
ChainMap({'a': 42}, {'x': 0, 'y': 0})
>>> c.maps[0]
{'a': 42}
>>> c['y']
0
```

- ChainMap은 여러개의 맵들을 가지는 콜렉션

```python
# contract.py
from collections import ChainMap

_contracts = { }

class Contract:
    @classmethod
    def __init_subclass__(cls):
        _contracts[cls.__name__] = cls
        
...

class BaseMeta(type):
    @classmethod
    def __prepare__(cls, *args):
        return ChainMap({}, _contracts)

    def __new__(meta, name, bases, methods):
        methods = methods.maps[0]
        return super().__new__(meta, name, bases, methods)


class Base(metaclass=BaseMeata):
    ...
    
...

```

- MetaClass 를 통해서 Class 선언 때 (```__prepare__```), annotation 값을 ChainMap에 저장
- Class 생성 때 (```__new__```) 를 통해서 checked 로직을 진행

```python
from contract import Base

class Player(Base):
    name: NonEmptyString
    x: Integer
    y: Integer

    def left(self, dx: PositiveInteger):
        self.x -= dx

    def right(self, dx: PositiveInteger):
        self.x += dx
```
``` python
>>> p = Player('Player', 0, 0)
>>> p.left(-10)
AssertionError: Must be > 0
```

- 실행결과: 잘 돌아간다!


Pycon2017에서 발표를 했던 David Beazley는 이렇게 Python에 있는 feature들을 사용하는 방법을 말하면서, 새로운 Package를 만들고, 기존의 프로젝트를 Reinvent 하길 권하고 있다!


## Reference

- [YouTube - The Fun of Reinvention (Screencast)](https://www.youtube.com/watch?v=js_0wjzuMfc)
- [Blog - The Fun Of Reinvention 정리](https://phillyai.github.io/2017-07-02-The-Fun-Of-Reinvention/)



