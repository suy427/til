자바에도 vector형 data type이 있다. (c++에도 있는건 아는데 써본적은 없음..ㅎ)      
얘는 가변길이의 배열인데 특이한건 객체를 담는게 아니라 객체의 주소값을 담아놓는 배열이다.      
그 말은 여러 다른 자료형의 객체를 담을 수 있다는거다. (primative type은 담을 수 없기 때문에 담으려면 Wrapper클래스를 써야한다)   
그리고 Vector는 ArrayList보다 더 원시적인 Collection이다!! (이제는 거의 쓰이지 않고 legacy에만 있다고 한다.)

그런데 막상 Vector를 선언하려고 보면 ```Vector<E>``` 이렇게 제네릭으로 담을 객체 타입을 지정해야하는데,   
이 Vector에 여러 다른 type의 객체를 담으려면 바로 사용할 수는 없고 Vector를 상속한 클래스를 만들어야한다.


--------------------

```java
public class MyVector extends Vector {
    pulic MyVector(int length) {
        super(length); // Vector 생성자의 의존성은 capacity 관련된거밖에 없다. length or length increment...
    }
}

public class VectorTest {

    public static void main(String[] args) {
        MyVector myVector = new MyVector(5);

	myVector.addElement("String Element");
	myVector.addElement(new Integer(10));
	myVector.addElement(new Double(4.2));

	// 이런게 가능... 왜 가능한지는 더 알아볼것..!
    }
}
```
