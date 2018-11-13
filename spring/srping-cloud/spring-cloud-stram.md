spring cloud stream
==========


#### links
- ref: https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/
- sample projects: https://github.com/spring-cloud/spring-cloud-stream-samples




message-drive microservic를 구축하기 위한 프레임워크.
메세지 브로커와 연결을 위해 spring-intergration 을 사용한다.


### use case

*@EnableBinding* broker에 연결할 수 있게 하는 어노테이션. 하나 이상의 input, output 채널을 구현한 인터페이스를 인자로 받는다. 스프링에서 기본적으로 제공하는 인터페이스는 sink, source, processor 가 있다.

*@StreamListener* 스트림 처리 이벤트를 수신하는 메서드에 추가

```java
@EnableBinding(BindingChannel::class)
class processor() {

  @StreamListener(Sink.INPUT)
  fun consume(Message message) {
    consumeMessage(message)
  }
}
```

binding channel interface 는 input, output 채널을 구현해야하고 각각 @Input, @Output 어노테이션을 이용해 채널을 정의한다.

```java
interface BindingChannel {
    companion object {
        const val TOPIC_A: String = "topic-a"
        const val TOPIC_A: String = "topic-b"
    }

    @Input(TOPIC_A)
    fun input(): SubscribableChannel

    @Output(TOPIC_B)
    fun output(): MessageChannel
}
```

-----

### Main Concepts
![SCSt-with-binder](https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/images/SCSt-with-binder.png)

Spring Cloud Stream 에 의해 주입된 input, output 채널을 통해 application 은 외부와 통신한다. 각 채널은 바인더를 통해 외부 브로커와 연결된다.

#### Binder 개념
Spring Cloud Stream 은 kafka 와 Rabbit MQ Binder Implementation 을 제공하고, 채널을 한정하지 않은 TestSupportBinder 도 제공한다.

Spring Boot를 이용한 설정과 Binder 개념은 Spring Cloud Stream application 이 middleware 에 유연하게 연결 할 수 있게 한다.
런타임에 채널이 연결될 카프카 `토픽 같은 destination을 동적으로 선택`할 수 있다.
스프링 부트에서 지원하는 `application.yml 같은 외부 설정 properties 를 통해 정의` 할 수 있다. 자동으로 classpath 의 binder 설정을 인지하고 사용한다. 어플리케이션에 여러 바인더를 패키징 할 수 있고 `런타임에 바인더를 결정`할 수 있다.

#### 강력한 pub-sub support


#### Consumer Groups

#### Consumer Types

#### Partitioning

--------
### Programming Model

![programming-model](https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/images/SCSt-overview.png)

메세징 시스템과의 communicate를 위한 기본 구조인 Message,
메세징 시스템과의 연결을 제공하는 컴포넌트인 Destination Binder,
Binder 와 어플리케이션의 컨슈머, 프로듀서의 브릿지 역할을 하는 Destination Bindings 가 주요 개념이다. 어플리케이션에서는 간단하게 아래처럼 설정해서 사용 할 수 있다.

Destination Binders
```
cloud:
    stream:
      binders:
        kafka:
          type: kafka
          environment:
            ...
```

Destination Bindings:
```
cloud:
  stream:
    binders:
      kafka-a:
        type: kafka
        environment:
          spring:
            cloud:
              stream:
                kafka:
                  binder:
                    brokers: ...
    bindings:
      topic-tistory-post:
        binder: kafka-a
        destination: topic-name
        contentType: application/json
        nativeDecoding: true
        group: group-name
        consumer:
          ...
        producer:
          ...
```
