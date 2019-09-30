# spring-data-redis

SDR은 Spring Data 의 한 모듈로, Spring application 에서의 쉬운 설정과 redis acces 를 제공한다. datastore 와의 상호작용을 위해 low, high 레벨의 추상화를 모두 제공하고, 하위 구조에 대해 유저가 신경쓰지 않도록 도와준다.

SDR framework는 레디스 저장소와 상호 작용하는 데 필요한 중복 작업과 보일러 플레이트 코드를 제거
* Spring-Data 는 스프링 프레임워크 핵심 기능들을 잘 사용하도록 설계되었음. IOC, resource 추상화, AOP 등등
* Spring-Data-Redis 는 connector 라이브러리로 Jedis, JRedis, SRP, Lettuce 를 제공


## Features
- low level 추상화된 **Connection Package** : Jedis, Lettuce 등 다양한 레디스 커넥션 드라이브
- Redis Driver Exception 을 Spring Data Access Exception 으로 translate
- high-level 추상화된 **RedisTemplate** 을 제공 : Redis Operation, Serialization, Exception Translation 을 수행
- **Pubsub 지원**
- Redis Sentinel and Redis Cluster support.
- **Reactive** API using the Lettuce driver.
- JDK, String, JSON and Spring Object/XML **mapping serializers**.
- **Atomic Counter** 나 **JDK Collection** 같은 **JDK intreface** 를 org.springframework.data.redis.support 패키지에 포함
 - **RedisList** 를 통한 collection methods 제공
 - atomic counter 를 통해 쉽게 **redis key incrementation**
- Sorting and Pipelining functionality.
- Dedicated support for SORT, SORT/GET pattern and returned bulk values.
- Automatic implementation of Repository interfaces including support for custom query methods using @EnableRedisRepositories.
- CDI support for repositories.


## Connect Redis
IOC 컨테이너에서 Redis 저장소에 접속하고, connector 에서의 어떤 액션들을 위해, SDR API 에는 하나의 세트가 필요하다.
* RedisConnection (org.springframework.data.redis.connection)
* RedisConnectionFactory - active 된 커넥션을 받고 작업을 진행


### RedisConnection
* building block 을 제공하여 레디스 백엔드 통신을 다룬다.
* 자동적으로 connectiing library exception 을 Spring exception 으로 전환
* RedisConnection 은 byte arroy 를 받고 리턴하는 로우레벨 메소드를 제공한다면, template 은 직렬화와 커넥션 관리를 해주는 하이레벨 메소드를 제공

### RedisConnectionFactory
* Active RedisConnection 은 RedisConnectionFactory를 통해서 생성됨.
    * 모든 커넥션은 스레드세이프한 native connection 을 공유한다.
* 팩토리는 PersistenceExceptionTranslator로 활동. (예외 전환)
* IocConnector  를 이용해 Conenector 라이브러리를 설정하고 사용하는 클래스에 주입.

### RedisTemplate
* 한번 설정되면, 템플릿은 스레드세이프하며 여러개의 인스턴스에서 재사용될수 있습니다.
* RedisCallback 을 이용해서 개발자가 직접 low-level redis 연산에 접근 할 수 있다.



---
### 기존 플젝에 lettuce library를 SDR로 전환하려다 롤백한 이유,,

- **Spring & Spring Boot multi project** 에서 **redis server 를 2대 이상 연결하는 경우**, Spring 설정은 쉽지만 Spring Boot 는 **RedisAutoConfiguration** 이 동작하기 때문에 (redisTemplate bean 을 가져와 자동 설정 - redisTemplate 이 2대 이상인 경우의 auto configuration을 아직 지원하지 않음,,) 연결을 위해서 더 많은 동작을 해주어야 한다. 이부분이 불필요하게 느껴지는 부분,,

- lettuce library에서 제공하는 async 메소드는 최상위 호출에서의 async 실행을 지원한다. redisTemplate에서도 async connection 을 지원하는데 method 자체를 async 하게 쓰려면 RedisFuture 를 반환하는 메소드를 새로 생성해야 한다.

- lettuce asyncCommand 의 명령 실행 동작
  - lettuce asyncCommand 에서 연속적으로 function을 호출 했을때,
    - 하나의 커넥션으로부터 각 command 생성하여 **순차적으로 dispatch 한다**
      - (state 와 complete 여부를 가지는 RedisCommand를 각각 생성)
    - RedisChannerWriter 에서 communication channel 에 command 를 푸시 (커멘드 실행)
    - command 실행 성공 여부를 기다리지 않으므로 성공적 수행여부를 보장하지 않는다.
    - 순차적으로 dispatch 하지만 async 하게 성공 응답을 기다리지 않으므로 여러 커맨드를 실행해도 수행 속도가 빠름.

- SDR 에서는 aysnc method 지원을 위해 template 단위에서 ReactiveConnection을 생성 후 Mono/Flux를 리턴하는 메소드를 제공하고 사용하길 권장 (하지만 실제 reactive 하게 쓰지 않는데 오버스펙인 느낌이었음)  



---
그 밖에,,

- Serializers
- Hash mapping
- Redis Transaction
- @Transactional 지원
- Pipelining
- Supported Classes
  - RedisList
  - RedisCacheManager
- Reactive Redis, Redis Cluster,,
- Redis repository
- domain entity
