그냥 설정파일이다. 확장자(ini)에서 알 수 있다싶이 Initialize할 값들을 저장해놓고  
이 갚들을 기반으로 무언가를 할 수 있다. 보통 Windows에서 많이 쓰는데 다른 OS에서도 쓸 수 있다.

------------
section, key, value의 영역이 있는데   

```shell
[섹션]1
키1=밸류1
키2=밸류2

[섹션2]
키3=밸류3
키4=밸류4
```
   
같은 형식이다.   
Win32 API(Linux에서는 Boost Library)에서 ini파일을 Parsing할 수 있는 함수들을 제공한다.


