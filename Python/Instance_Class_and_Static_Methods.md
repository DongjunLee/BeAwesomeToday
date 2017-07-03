# Instance, Class, and Static Methods

## Instance Methods

- 인스턴스 메소드는 **'self'** 인 인스턴스를 인자로 받고 인스턴스 변수와 같이 하나의 인스턴스에만 한정된 데이터를 생성, 변경, 참조


## Class Methods

- 클래스 메소드는 **'cls'** 인 클래스를 인자로 받고 모든 인스턴스가 공유하는 클래스 변수와 같은 데이터를 생성, 변경 또는 참조하기 위한 메소드
- @classmethod 데코레이터를 사용해서 정의


## Static Methods

- 스태틱 메소드는 클래스 안에서 정의되어 클래스 네임스페이스 안에는 있을뿐 일반 함수와 전혀 다를바가 없음.
- @staticmethod 데코레이터를 사용해서 정의

## Example

```python
class A(object):
    def foo(self,x):
        print "executing foo(%s,%s)"%(self,x)

    @classmethod
    def class_foo(cls,x):
        print "executing class_foo(%s,%s)"%(cls,x)

    @staticmethod
    def static_foo(x):
        print "executing static_foo(%s)"%x    

a=A()
```

```python
>>> obj = MyClass()
>>> obj.method()
('instance method called', <MyClass instance at 0x101a2f4c8>)

>>> MyClass.method(obj)  # 이런 식으로 instance를 넣어서 사용하는 것도 가능.
('instance method called', <MyClass instance at 0x101a2f4c8>)

>>> obj.classmethod()
('class method called', <class MyClass at 0x101a2f4c8>)

>>> obj.staticmethod()
'static method called'
```

다음은 classmethod 를 생성자로 사용하는 간단한 예제입니다.

```python

# -*- coding: utf-8 -*-
class Person(object):
    
    my_class_var = 'sanghee'
    
    def __init__(self, year, month, day, sex):
        self.year = year
        self.month = month
        self.day = day
        self.sex = sex
        
    def __str__(self):
        return '{}년 {}월 {}일생 {}입니다.'.format(self.year, self.month, self.day, self.sex)
    
    @classmethod
    def ssn_constructor(cls, ssn):
        front, back = ssn.split('-')
        sex = back[0]
        
        if sex == '1' or sex == '2':
            year = '19' + front[:2]
        else:
            year = '20' + front[:2]
            
        if (int(sex) % 2) == 0:
            sex = '여성'
        else:
            sex = '남성'
            
        month = front[2:4]
        day = front[4:6]
        
        return cls(year, month, day, sex)
    
    @staticmethod
    def is_work_day(day):
        # weekday() 함수의 리턴값은
        # 월: 0, 화: 1, 수: 2, 목: 3, 금: 4, 토: 5, 일: 6
        if day.weekday() == 5 or day.weekday() == 6:
            return False
        return True
    
ssn_1 = '900829-1034356'
ssn_2 = '051224-4061569'

person_1 = Person.ssn_constructor(ssn_1)
print(person_1)

person_2 = Person.ssn_constructor(ssn_2)
print(person_2)

import datetime
# 일요일 날짜 오브젝트 생성
my_date = datetime.date(2016, 10, 9)

# 클래스를 통하여 스태틱 메소드 호출
print(Person.is_work_day(my_date))
# 인스턴스를 통하여 스태틱 메소드 호출
print(person_1.is_work_day(my_date))

>>> 1990년 08월 29일생 남성입니다.
>>> 2005년 12월 24일생 여성입니다.
>>> False
>>> False

```

- 위와 같이 classmethod 는 새로운 intance를 정의하고 만드는 것에 좋다. 모든 인스턴스가 가지는 값을 변경할 수도 있어 흔히 factory pattern에 자주 사용 됨.
- staticmethod는 해당 클래스에 정의함으로서 역할에 대해서 잘 정의할 수 있는 장점.


## Reference

- [Real Python - Instance, Class, and Static Methods — An Overview
](https://realpython.com/blog/python/instance-class-and-static-methods-demystified/)
- [파이썬 - OOP Part 4. 클래스 메소드와 스태틱 메소드 (Class Method and Static Method)](http://schoolofweb.net/blog/posts/%ED%8C%8C%EC%9D%B4%EC%8D%AC-oop-part-4-%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%A9%94%EC%86%8C%EB%93%9C%EC%99%80-%EC%8A%A4%ED%83%9C%ED%8B%B1-%EB%A9%94%EC%86%8C%EB%93%9C-class-method-and-static-method/)
- [Stack overflow - What is the difference between @staticmethod and @classmethod in Python?](https://stackoverflow.com/questions/136097/what-is-the-difference-between-staticmethod-and-classmethod-in-python)



