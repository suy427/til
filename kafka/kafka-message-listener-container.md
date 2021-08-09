### ```KafkaMessageListenerContainer<K, V>``` Class 분석
* ```KafkaConsumer<K, V>```가 thread-safe하도록 보장
* 컨슈머의 LifeCycle및 상태 관리
* 토픽파티션 수동/자동 할당 설정
* 여러가지 commit모드 지원
* 컨슈머 상태에 따른 다양한 Listener 지원 (idle, repartition, paused, resume 등..)  

과 같은 기능이 구현되어있는 ConsumerContaine이다.
   
----

#### Hierarchy
```AbstractMessageListenerContainer<K, V>```를 상속하고, ```doStart()```, ```doStop()```외에는 모두 부모클래스에 구현되어있다.
```doStart()```, ```doStop()```는 부모클래스가 구현하고있는 ```LifeCycle```인터페이스의 ```start()```, ```stop()```메서드를 파사드 패턴으로 내부 구현한 형태이다.
따라서 Spring이 뜰때 ```doStart()```, ```doStop()```가 이 클래스의 시작점과 끝점이 된다.

#### Components
주요 컴포넌트로는 ```ListenerConsumer listenerConsumer```, ```ListenableFuture<?> listenerConsumerFuture```, ```CountDownLatch startLatch```가 있다.

* ```listenerConsumerFuture```  
  - listenerConsumer를 실행시키는 Thread Executor.
* ```startLatch```  
  - ```listenerConsumer```를 thread-safe하게 구동하기 위한 latch.  
   
__--> 기본적으로 KafkaMessageListenerContainer는 단일 thread로 consumer를 동작시킨다. 이 객체를 list형태로 가지고 concurrent하게 동작하는 ```ConcurrentMessageListenerContainer```도 있다.__

* ```listenerConsumer``` (Signature: ```ListenerConsumer implements SchedulingAwareRunnable, ConsumerSeekCallback```)  
  KafkaConsumer클래스를 Listenable한 형태로 wrapping한 클래스. ```KafkaMessageListenerContainer```의 inner class이며,  
  ```KafkaMessageListenerContainer```에서 ```doStart()```가 호출되면, 가지고 있던 listener객체 및 여러가지 설정들을 주입해서 instanciate한다.
  
