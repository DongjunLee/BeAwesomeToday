## Data Structures in Python 3

### 1. typing.NamedTuple

- added in Python 3.5 and apply type hints 3.6
- nicer syntax, inheritance, type annotations, default values(3.6.1), equally fast
- example

```python
from typing import NamedTuple

class Student(NamedTuple):
   name: str
   address: str
   age: int
   sex: str

>>> tommy = Student(name='Tommy Johnson', address='Main street', age=22, sex='M')
>>> tommy
Student(name='Tommy Johnson', address='Main street', age=22, sex='M')
```

The *NamedTuple* class is a subclass of tuple.

```python
>>> isinstance(tommy, tuple)
True
>>> tommy[0]
'Tommy Johnson'
``` 

using default values

```python
class MaleStudent(Student):
   sex: str = 'M'  # default value, requires Python >= 3.6.1

>>> MaleStudent(name='Tommy Johnson', address='Main street', age=22)
MaleStudent(name='Tommy Johnson', address='Main street', age=22, sex='M')  # note that sex defaults to 'M'
```

### 2. typing.Optional

- added in Python 3.6
- Optional Type
- Optional[X] is equivalent to Union[X, None].
	- Union: Union type; Union[X, Y] means either X or Y.
	- example
	
	```python 
	Union[Union[int, str], float] == Union[int, str, float]
	Union[int] == int  # The constructor actually returns int
	Union[int, str, int] == Union[int, str]
	Union[int, str] == Union[str, int]
	Union[int, object] == object
	```

### 3. types.MappingProxyType

- added in Python 3.3
- be used as a read-only dict
- example

```python
>>> from  types import MappingProxyType
>>> data = {'a': 1, 'b':2}
>>> read_only = MappingProxyType(data)
>>> del read_only['a']
TypeError: 'mappingproxy' object does not support item deletion
>>> read_only['a'] = 3
TypeError: 'mappingproxy' object does not support item assignment
```

- it helps us avoid a whole class of difficult-to-find bugs.
- Note though that while read_only is read-only, it is not immutable, so if you change data, read_only will change too:

```python
>>> data['a'] = 3
>>> data['c'] = 4
>>> read_only  # changed!
mappingproxy({'a': 3, 'b': 2, 'c': 4})
```

### 4. types.SimpleNamespace

- added in Python 3.3
- provides attribute access to its namespace as well as a meaningful repr.
- example

```python
>>> from types import SimpleNamespace

>>> data = SimpleNamespace(a=1, b=2)
>>> data
namespace(a=1, b=2)
>>> data.c = 3
>>> data
namespace(a=1, b=2, c=3)
```

- I sometimes use this as an easier-to-read-and-write alternative to dict. More and more though, I subclass it to get the flexible instantiation and repr output for free:

```python
>>> import random

>>> class DataBag(SimpleNamespace):
>>>    def choice(self):
>>>        items = self.__dict__.items()
>>>        return random.choice(tuple(items))

>>> data_bag = DataBag(a=1, b=2)
>>> data_bag
DataBag(a=1, b=2)
>>> data_bag.choice()
(b, 2)
```

### 5. Enum

- new in Python 3.4
- enumeration is a set of symbolic names
- can be iterate
- example

```python
from collections import namedtuple
from enum import Enum

class HairColor(Enum):
    blonde = 1
    brown = 2
    black = 3
    red = 4

Person = namedtuple('Person', ['name','age','hair_color'])
bert = Person('Bert', 5, HairColor.black)

>>> print(bert.name)
Bert
>>> print(bert.age)
5
>>> print(bert.hair_color)
HairColor.black
>>> print(bert.hair_color.value)
3
```

- There are Enum, IntEnum, IntFlag, Flag, unique(), auto.
- New in version 3.6: Flag, IntFlag, auto

```python
>>> from enum import Flag, auto
>>> class Color(Flag):
...     RED = auto()
...     BLUE = auto()
...     GREEN = auto()
...     WHITE = RED | BLUE | GREEN
...
>>> Color.WHITE
<Color.WHITE: 7>
```

### 6. Counter

- dict subclass for counting hashable objects
- Elements are counted from an iterable or initialized from another mapping (or counter):

```python
>>> c = Counter()                           # a new, empty counter
>>> c = Counter('gallahad')                 # a new counter from an iterable
>>> c = Counter({'red': 4, 'blue': 2})      # a new counter from a mapping
>>> c = Counter(cats=4, dogs=8)   
>>>
```

- elements()

```python
>>> c = Counter(a=4, b=2, c=0, d=-2)
>>> sorted(c.elements())
['a', 'a', 'a', 'a', 'b', 'b']
```

- mathematical operations

```python
>>> c = Counter(a=3, b=1)
>>> d = Counter(a=1, b=2)
>>> c + d                       # add two counters together:  c[x] + d[x]
Counter({'a': 4, 'b': 3})
>>> c - d                       # subtract (keeping only positive counts)
Counter({'a': 2})
>>> c & d                       # intersection:  min(c[x], d[x]) 
Counter({'a': 1, 'b': 1})
>>> c | d                       # union:  max(c[x], d[x])
Counter({'a': 3, 'b': 2})
```

### 7. OrderedDict

- added in Python 3.1
- dict subclass, supporting the usual dict methods.
- a dict that remembers the order that keys were first inserted
- example

```python
>>> # regular unsorted dictionary
>>> d = {'banana': 3, 'apple': 4, 'pear': 1, 'orange': 2}

>>> # dictionary sorted by key
>>> OrderedDict(sorted(d.items(), key=lambda t: t[0]))
OrderedDict([('apple', 4), ('banana', 3), ('orange', 2), ('pear', 1)])

>>> # dictionary sorted by value
>>> OrderedDict(sorted(d.items(), key=lambda t: t[1]))
OrderedDict([('pear', 1), ('orange', 2), ('banana', 3), ('apple', 4)])

>>> # dictionary sorted by length of the key string
>>> OrderedDict(sorted(d.items(), key=lambda t: len(t[0])))
OrderedDict([('pear', 1), ('apple', 4), ('orange', 2), ('banana', 3)])
```


## Reference

- [New interesting data structures in Python 3](https://github.com/topper-123/Articles/blob/master/New-interesting-data-types-in-Python3.rst)
- [Enum Python 3.6.1 Documentation](https://docs.python.org/3/library/enum.html)
- [collections Python 3.6.1 Documetation](https://docs.python.org/3/library/collections.html#collections.Counter)



