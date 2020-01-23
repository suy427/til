# MultiValueMap

그냥 이름만 보고 예상했을 때는 여러 다른 ‘타입’의 값들을 넣을 수있는 맵인가..?(Python처럼) 생각했는데   
그게아니고 하나의 Key에 대해서 여러개의 값(Value)을 가질 수 있는 Map이다.    
Java에서 지원하는 자료구조는 아니고 Spring에서 지원하는 애다.   
보통 Http Request나 Response시에 헤더값을 비롯한 여러 값들을 저장하기 위해서 쓰이는 것 같다.   

```
// common map
map.put(‘a’, 1)
map.put(’a’, 2) 
System.out.println(map.get(‘a’)) // 2 —> 나중에 들어간 값으로 바뀜


// multi value map
map.put(‘a’, 1)
map.put(‘a’, 2)
System.out.println(map.get(‘a’)) // [1, 2] —> 두개의 값을 모두 가짐.
```
