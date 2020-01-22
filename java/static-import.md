# static-import

static method가 객체 선언 없이 Class.method()로 호출될수 있는데  
```import static package.class.method```   
이런식으로 static import를 하면 앞에 객체를 명시할 필요 없이 해당 method를 쓸 수 있다.  
단, 동일 클래스 내에 이름이 같은 method가 있으면 자기자신의 method가 우선이다.
