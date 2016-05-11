## Pickle Protocol

pickle은 Python에서 사용되는 직렬화라이브러리.

pickle을 만드는 것에는 3가지 protocol 들이 있음.(Python 2 - default : 0)


- **Protocol version 0** : ASCII 프로토콜로서 이전 Python 버전과 호환됨(Readable)
- **Protocol version 1** : Old Binary 포맷으로 이전 Python 버전과 호환됨
- **Protocol version 2** : Python 2.3 에서 나온 프로토콜로서 조금 더 효율적인 새로운 스타일의 pickling을 제공함.

**Python3**

5가지 protocol이 존재(0 ~ 4) 위 3가지 포함, default = 3 (protocol = 2에 비해 2배는 빠르다고 함)

- **Protocol version 3** : bytes object를 지원, Python 2.x 와 호환불가
- **Protocol version 4** : 더 크고, 많은 종류의 객체를 지원

#### Example

```python
import numpy as np
import pickle
class data(object):
    def __init__(self):
        self.a = np.zeros((100, 37000, 3), dtype=np.float32)

d = data()
print "data size: ", d.a.nbytes/1000000.
print "highest protocol: ", pickle.HIGHEST_PROTOCOL
pickle.dump(d,open("noProt", 'w'))
pickle.dump(d,open("prot0", 'w'), protocol=0)
pickle.dump(d,open("prot1", 'w'), protocol=1)
pickle.dump(d,open("prot2", 'w'), protocol=2)


out >> data size:  44.4
out >> highest protocol:  2

# noProt: 177.6MB
# prot0: 177.6MB
# prot1: 44.4MB
# prot2: 44.4MB
```

ASCII 프로토콜을 사용하면 용량이 더 커짐


```python 
protocol=pickle.HIGHEST_PROTOCOL or -1
```

최신 protocol을 사용하고 싶다면.



