spring cloud stream
==========


##### links
- ref: https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/
- sample projects: https://github.com/spring-cloud/spring-cloud-stream-samples




message-drive microservic를 구축하기 위한 프레임워크.
메세지 브로커와 연결을 위해 spring-intergration 을 사용.

 Spring Cloud Stream은 독립 실행 형 생산 등급 봄 응용 프로그램을 만들기 위해 Spring Boot을 기반으로하며 Spring Integration을 사용하여 메시지 브로커에게 연결성을 제공합니다. 그것은 여러 공급 업체의 미들웨어에 대한 독창적 인 구성을 제공하여 지속적인 게시 구독 의미론, 소비자 그룹 및 파티션의 개념을 소개합니다.


@EnableBinding  이용해 broker에 연결할 수 있고
@StreamListener 스트림 처리 이벤트를 수신하는 메서드에 추가할 수 있습니다. 다음 예제는 외부 메시지를 수신하는 싱크 응용 프로그램을 보여줍니다.
