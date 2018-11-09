spring cloud stream
==========


#### links
- ref: https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/
- sample projects: https://github.com/spring-cloud/spring-cloud-stream-samples




message-drive microservic를 구축하기 위한 프레임워크.
메세지 브로커와 연결을 위해 spring-intergration 을 사용.

 Spring Cloud Stream은 독립 실행 형 생산 등급 봄 응용 프로그램을 만들기 위해 Spring Boot을 기반으로하며 Spring Integration을 사용하여 메시지 브로커에게 연결성을 제공합니다. 그것은 여러 공급 업체의 미들웨어에 대한 독창적 인 구성을 제공하여 지속적인 게시 구독 의미론, 소비자 그룹 및 파티션의 개념을 소개합니다.


### use case

@EnableBinding  이용해 broker에 연결할 수 있게 하는 어노테이션. 하나 이상의 input, output 채널을 구현한 인터페이스를 인자로 받는다. 스프링에서 기본적으로 제공하는 인터페이스는 sink, source, processor 가 있다.
@StreamListener 스트림 처리 이벤트를 수신하는 메서드에 추가할 수 있습니다.

```java
@EnableBinding(Sink::class)
class processor() {

  @StreamListener(Sink.INPUT)
  fun consume(Message message) {
    consumeMessage(message)
  }
}
```

binding channel interface 는 input, output 채널을 구현해야하고 각각 @Input, @Output 어노테이션을 이용해 채널을 정의한다.

```java
interface TistoryPostCategoryProcessor {
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

#### 강력한 pub-sub support

#### Consumer Groups

#### Consumer Types

#### Partitioning
