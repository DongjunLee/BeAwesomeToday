# Iterator Pattern

## Class Diagram

![class_diagram](./1.Iterator.png)


Aggregator : 자료구조  
Creates로 자신을 넘겨주기 때문에 포함관계로서 자료구조를 가진다.  

## Example

```javascript
	
	
	arr = [1, 2, 3, 4, 5]
	
	// 1번
	arr.forEach(function() {
		...
	});
	
	// 2번
	
	for (var i =0; i < arr.length; i++) {
		...
	}
	
```

## 역할
- Aggregator/Iterator가 누구인가?

## 장점

- 자료구조의 입장에서 공급과 소비를 분리할 수 있게 해준다.

## 고려할 사항들
- Thread Issue
- Atomic Integer
- Concurrent Modification Exception

## Reference

[Java 언어로 배우는 디자인 패턴 입문](http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=2104376)
