# spring boot logging system

* spring boot는 JCL(Jakarta Commons Logging)으로 Logging을 구현하는데 얘는 그 자체로 Logger구현체가 아니라   
  여러 실제 구현체 Logger를 갖다 쓸 수 있도록 해놓은 interface이다. 원하는 Logger구현체의 LogFactory만 맞춰주면
  별다른 코드 수정 없이 얼마든지 다른 구현제로 교체도 할 수 있다. interface인 ```JCL이 실제 구현체를 선택하는 시점은   
  런타임인데 이 때문에 클래스 로더에 의존적이고 결과적으로 GC가 제대로 작동하지 않는다.```   
  이 때 등장한게 SLF4J(Simple Logger Facade for Java)인데 얘는 컴파일 시점에 구현체를 가져온다.
  SLF4J가 기본값으로 선택하는 구현체는 Logback이다.

## Logback

- Logback은 Spring boot 기본 log구현체이므로 Spring boot를 의존성에 추가해뒀다면 따로 의존성을 추가할 필요가 없다.   
  (spring-boot-starter-web 안에 spring-boot-starter-logging이라는곳에 있다.)

- 일반 spring이나 java프로그램은은 logback.xml파일을 통해 logback의 설정과 관리를 한다.   
  (logback이 설정파일을 찾는 순서 : logback.groovy -> logback-test.xml -> logback.xml)
  하지만 spring boot는 logback-spring.xml을 통해 관리한다.
  ```spring boot가 설정되기 전에 logback.xml이란 이름을 가지고 logback관련 설정을 해버리기 때문에 logback.xml이 아니라 logback-spring.xml을 쓰는 것이다.```

### Configuration
  - application.yml
    ```yaml
      logging:
        level:
         org.springframework: INFO
         org.springframework.web: DEBUG
    ```   
    이렇게 yml형식으로 디버그 수준 설정이 가능하다
    
  - logback-spring.xml
    * appender : log를 어디에, 어떻게 찍을지 설정하는 부분(console, DB, stdout, file, socket 등 여러 매체에 따른 설정 가능...)   
    * logger : 정의된 appender를 가지고 해당 logger가 사용될 package와 log level을 지정(Track > DEBUG > INFO > WARN > ERROR)    
