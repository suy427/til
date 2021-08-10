# ```ListenerConsumer``` Class 분석 (1)
사실상 ```KafkaMessageListenerContainer```의 본체로, inner class로 선언되어있다. ```KafkaMessageListenerContainer```는 이 객체의 상태 관리 및 설정을 갖고있는 Container역할이다.   


이름처럼 ```MessageListener```와 ```ListenerType```을 가지며 크게 ```MessageListener```, ```BatchMessageListener```로 나뉜다. 이 또한 이름처럼 단일 메세지에 대한 Listener인지 Batch Message에 대한 Listener인지로 구분한다.
</br>  
     
## Functions
----
### Listener ACK
Listener차원에서의 consumerRecord에 대한 commit이라고 생각하면 된다. (내부적으로 결국 ```consumer.commitSync()``` or ```consumer.commitAsync()```를 사용한다.)  
consumer.poll()이 ConsumerRecords를 반환할 때마다 각각의 ConsumerRecord에 대해 messageListener가 호출된다.
쉽게 생각하면 commit을 하는 여러가지 모드를 지원한다고 생각하면 된다.

|모드|설명|
|---|---|
|```RECORD```|listener가 하나의 record를 처리한 후 commit|
|```BATCH```|consumer.poll() 에서 반환된 모든 records를 다 처리 후 commit|
|```TIME```|Commit the offset when all the records returned by the poll() have been processed, as long as the ackTime since the last commit has been exceeded|
|```COUNT```|Commit the offset when all the records returned by the poll() have been processed, as long as ackCount records have been received since the last commit|
|```COUNT_TIME```|Similar to TIME and COUNT, but the commit is performed if either condition is true|
|```MANUAL```|```MessageListener```가 직접 ```Acknowledge.acknowledge()```를 호출한다. 그 후에는 ```BATCH```와 같다|
|```MANUAL_IMMEDIATE```|```MessageListener```가 직접 ```Acknowledge.acknowledge()```를 호출하면 즉시 record를 커밋한다|
   
</br>
   
__TIME & COUNT & COUNT_TIME__   
```pollAndInvoke()``` > ```processCommits()```의 로직을 보면 ```TIME```, ```COUNT```에 대한 로직이 있다.

```java
boolean countExceeded = this.isCountAck && this.count >= this.containerProperties.getAckCount();
if ((!this.isTimeOnlyAck && !this.isCountAck) || countExceeded) {
    if (this.isCountAck) {
        this.logger.debug(() -> "Committing in " + ackMode.name() + " because count "
        + this.count
        + " exceeds configured limit of " + this.containerProperties.getAckCount());
    }
    commitIfNecessary();
    this.count = 0;
}
else {
    timedAcks(ackMode);
}
```

</br>

__MANUAL & MANUAL_IMMEDIATE__   
ListenerConsumer의 ```doInvokeOnMessage()```에는 아래와 같은 로직이 있다. listenerType에 따라 메세지 처리를 달리한다.
아래 코드를 보면 listenerType이 ```ACKNOWLEDGING```종류인 경우 ack처리를 listener가 명시적으로 처리하는 것을 알 수 있다.
ACK모드가 MANUAL or MANUAL_IMMEDIATE인 경우 ```AcknowledgineMessageListener```를 구현하여 ack 처리를 직접 해줘야한다.
```java
switch (this.listenerType) {
  case ACKNOWLEDGING_CONSUMER_AWARE:
    this.listener.onMessage(record,
    this.isAnyManualAck ? new ConsumerAcknowledgment(record) : null, this.consumer);
    break;
						
  case CONSUMER_AWARE:
    this.listener.onMessage(record, this.consumer);
    break;
						
  case ACKNOWLEDGING:
    this.listener.onMessage(record,
    this.isAnyManualAck ? new ConsumerAcknowledgment(record) : null);
    break;
						
  case SIMPLE:
    this.listener.onMessage(record);
    break;
}
```

