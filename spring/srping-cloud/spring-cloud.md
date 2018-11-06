Spring-Cloud
=======

- 분산환경에서 나타나는 여러 패턴의 main projects 제공으로 분산 시스템 환경에서 빠르게 application 을 빠르게 구성하고 빌드
- latop, metal data center, cloud foundry platform 등 어떤 분산 환경에서도 잘 동작

----
### features

- Distributed/versioned configuration
- Service registration and discovery
- Routing
- Service-to-service calls
- Load balancing
- Circuit Breakers
- Global locks
- Leadership election and cluster state
- Distributed messaging


---
### main projects

##### Spring Cloud Config
git repository 로 외부 configuration 을 관리.

##### Spring Cloud Stream
외부 시스템에 연결 할 수 있는 application 을 빠르게 구축하기 위한 light한 event-driven microservices framework.  
##### Spring Cloud Stream Starter
스프링부트 기반 외부 시스템 integration을 제공하는 [Spring Integration applications](https://www.slideshare.net/WangeunLee/spring-integration-47185594)

##### Spring Cloud Dataflow
DSL, dnd GUI, REST-API 의 사용으로 data pipeline 기반 microservice 의  data orchestration 을 용이하게함.
