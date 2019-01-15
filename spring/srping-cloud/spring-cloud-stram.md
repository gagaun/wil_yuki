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

#### 강력한 publish-subscribe support
응용 프로그램 간의 통신은 공유된 topic을 통해 데이터가 broadcast되는 publish-subscribe 모델을 따른다.

http endpoint 에서 감지한 data 는 공통 destination 인 raw-sensor-data로 보내진다. 마이크로 서비스 응용 프로그램과 원시 데이터를 HDFS (Hadoop Distributed File System)로 수집하는 다른 마이크로 서비스 응용 프로그램에 의해 독립적으로 처리된다. 데이터를 처리하기 위해 런타임에 topic 이  input 로 명시된다.






#### Consumer Groups

#### Consumer Types

#### Partitioning

--------
### Programming Model

![programming-model](https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/images/SCSt-overview.png)

메세징 시스템과의 communicate를 위한 기본 구조인 Message,
메세징 시스템과의 연결을 제공하는 컴포넌트인 Destination Binder,
Binder 와 어플리케이션의 컨슈머, 프로듀서의 브릿지 역할을 하는 Destination Bindings 가 주요 개념이다. 어플리케이션에서는 간단하게 아래처럼 설정해서 사용 할 수 있다.

#### Destination Binders
```
cloud:
    stream:
      binders:
        kafka:
          type: kafka
          environment:
            ...
```


#### Destination Bindings:
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

#### Producing and Consuming Messages

##### 기존 interface 활용

Spring Cloud Stream 은 Enterprise Integration Patterns에 기초해서 세워졌고, 이미 사용자들에게 익숙한 Spring Integration의 내부 Implementation에 의존한다.

예를 들어, Source 어노테이션을 MessageSource의 output channel 로 붙일 수 있고, 친숙한 @InboundChannelAdapter 도 사용할 수 있다.
```java
@EnableBinding(Source::class)
class TimeSource {

  @Bean
  @InboundChannelAdapter(value = Source.OUTPUT, poller = @Poller(fixedDelay = "10", maxMessagesPerPoll = "1"))
  fun timerMessageSource(): MessageSource<String> {
    return () -> new GenericMessage<>("test")
  }
}

```
비슷한 방식으로 Processor 바인딩을 위 @Transformer 나 @ServiceAdaptor 도 쓸 수 있다.

##### @StreamListener 어노테이션
Spring Integration의 인터페이스를 보완해서, Spring Cloud Stream은 다른 메세징 어노테이션에 기반한 @StreamListener 어노테이션을 제공한다. content-based routing 등등의 편의성을 제공한다.

```java
@EnableBinding(Sink.class)
public class VoteHandler {

  @Autowired
  VotingService votingService;

  @StreamListener(Sink.INPUT)
  public void handle(Vote vote) {
    votingService.record(vote);
  }
}
```
다른 메세징 어노테이션들처럼, 메소드 변수들은 @Payload, @Headers, @Header 어노테이션을 붙일 수 있다.
데이터를 리턴하기 위해서는 반드시 output binding destination을 특정하는 @SendTo 어노테이션을 써야한다.


##### Content-based routing 을 위한 @StreamListener 사용
@StreamListener 어노테이션을 사용한 다수의 핸들러 메소드로 메세지가 dispatching 될 수 있도록 지원한다. 단 메소드는 아래 조건을 충족해야 한다.
- 값을 리턴해야 한다.
- ㅁㅔ소드가 핸들링하는 단위는 개별 메세지다. reactive api method 는 지원되지 않는다.


consuming 조건은 어노테이션의 condition argument 에 SpEl 표현식으로 정의되고, 각 메세지를 평가한다. 모든 조건에 부합하는 핸들러는 같은 스레드에서 호출되고, 호출 순서는 보장하지 않는다.
```java
@EnableBinding(Sink.class)
@EnableAutoConfiguration
public static class CatsAndDogs {

    @StreamListener(target = Sink.INPUT, condition = "payload.class.simpleName=='Dog'")
    public void bark(Dog dog) {
       // handle the message
    }

    @StreamListener(target = Sink.INPUT, condition = "payload.class.simpleName=='Cat'")
    public void purr(Cat cat) {
       // handle the message
    }
}
```

##### Using Polled Consumer
PolledConsumer binding 채널로 pollableMessageSource가 필요하다.
```java
public interface PolledConsumer {
  @Input
  PollableMessageSource destIn();

  @Output
  MessageChannel destOut();
}
```

PollableMessageSource.poll() 메소드는 MessageHandler 를 인자로 가지는데, 람다로 표현된다. recieve 와  process 를 성공하면 true 를 리턴한다.
MessageHanlder 에서 에러가 발생하면 errorChannel 로 message 가 publish된다.

-----


### error handling

#### Application error handling
*  consume errorChannel

#### System error handling
* Drop failed messages
    * 추가적인 시스템 레벨 설정이 없으면 디포트로 메시징 시스템은 fail message 를 drop한다.
    * Message 손실을 피하기 위해 복구 매커니즘 recovery mechanism 이 필요하다.
* DLQ - Dead Letter Queue
    *
```
spring.cloud.stream.rabbit.bindings.input.consumer.auto-bind-dlq=true
```
    * 위 설정을 하면 모든 실패 메세지는 이 큐로 라우팅 된다. Original error 에 연관된 stack trace 를 볼수 없는데, 이에 대한 정보를 더 얻기 위해서는 다음 설정을 한다. Internal error handler가 error message 를 intercept 해서 dlq에 publishing 하기 전에 부가 정보를 더한다.
    *
```
spring.cloud.stream.rabbit.bindings.input.consumer.republish-to-dlq=true
```
* Re-queue failed messages
    * 현재 제공되는 binder (rabbit, kafka)는 message processing 을 위해 retrytemplate에  의존한다.
    * Max-attempts 가 1로 설정된 경우에는 메세지의 내부적 reprocessing 이 disable된다.
Failed message 를 다시 큐에 넣으면, 즉시 original handler 로 보내지고, 효과적으로 retry loop 를 생성하게 된다.
    * Internal retry 를 disable 하기 위해 max-attempts = 1 로 꼭 설정 되어야 하고, requeue-rejected 는 true로 설정되어야 한다. 이 설정이 되면 Failed message는 동일한 핸들러로 resubmit 되고, 핸들러 내에 자체적으로 retry 로직을  수행할 수 있다. 핸들러가 AmqpRejectAndDontRequeueException 을 던질때까지 계속해서 반복한다.
*  Retry template
    * Retry template 은 spring retry 라이브러리의 일부분.  
    *  Retry template 관련 consumer properties
        * maxAttempts  the number of attempts to process the message. Default 3
        * backOffInitialinterval : The backoff initial interval on retry. Default 1000ms
