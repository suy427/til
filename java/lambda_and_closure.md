## lambda VS closure (in Java)

'폐쇄' 라는 뜻을 가지는 Closure라는 이름에서 알 수 있듯이,  
둘의 차이는 scope에 달려있다.

lambda는 결국 함수처럼 생긴 interface '객체'이고   
closure는 함수처럼 생긴 '익명 클래스' 이다.   
lambda와 closure 둘 다 자신을 둘러싼 context의 변수를 가져다 쓸 수 있는데,  
lambda가 일반 method와 비슷하게 그 변수를 매개변수로써 참조하는 것과는 다르게   
closure는 그 값을 복사해서 final로 쓴다. 즉 closure내부는 외부로부터 '폐쇄' 되어있다는 것이다.   
