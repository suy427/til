enumerate라는 interface가 있는데 interface라 객체 생성은 안되고 vertor로 부터 생성될 수 있다.  
컬렉션 처럼 여러 element를 담고있지만 Collection을 구현하지는 않는다.   
iterator처럼 원소를 순회하며 처리할 일이 있을 때 쓸 수 있다.

```boolean hasMoreElements()``` 와 ```<E> nextElement()``` 메소드가 있다.
