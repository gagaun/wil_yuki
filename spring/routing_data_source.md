## AbstractDataSource with Kotlin, SpringBoot2, JPA


SpringBoot2 에서 하나 이상의 DataSource 를 동적으로 연결하고 싶은 경우, AbstractDataSource 를 사용할 수 있다.
Kotlin, JPA 와 함께 사용하는 법을 간단히 정리해본다.


### Dependencies
```
classpath("org.jetbrains.kotlin:kotlin-noarg:${kotlinVersion}")

apply plugin: 'kotlin-jpa'

implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
implementation 'org.springframework:spring-jdbc'

runtimeOnly 'mysql:mysql-connector-java'
```
- http://kotlinlang.org/docs/reference/compiler-plugins.html#all-open-compiler-plugin


### 1. AbstractRoutingDataSource 구현
#### targetDataSources
- 라우팅 대상이 되는 프로퍼티로, dataSource name 을 key 로, dataSource 를 구현한 객체를 value 로 가지는 map
- datasource routing 시점마다 determineCurrentLookupKey 를 수행하여 key값을 얻고, targetDataSources에서 해당 key값의 dataSource를 얻어와 routing 한다.

```
@Bean
fun dataSource(): DataSource {
    val dataSource = RoutingDataSource()
    val targets = mapOfDataSources()
    dataSource.setTargetDataSources(targets)
    return dataSource
}

fun mapOfDataSources(): Map<String, DataSource> {
  ...
  // 라우팅 대상이 될 dataSource 를 정의한다.
  // key - dataSource name
  // value - DataSource 구현 객체
}
```

#### determineCurrentLookupKey
- AbstractRoutingDataSource 를 구현한 클래스는 반드시 이 메소드를 override 하여 구현해야 한다.
- Jdbc connection 을 생성하거나 get할때 determineTagetDataSource 를 호출하고
이때 현재 context의 key(dataSource name) 를 판단하기 위해 determineCurrentLookupKey를 호출한다.

```
class RoutingDataSource : AbstractRoutingDataSource() {
    override fun determineCurrentLookupKey(): Any? {
        return RoutingDataSourceContext.getCurrentDataSourceName()
    }
}
```

- ThreadLocal을 가지는 객체를 구현하여 AbstractRoutingDataSource 에서 라우팅할 DataSource 의 key 값인 현재 context 의 dataSourceName 을 지정한다.

```
class RoutingDataSourceContext {
    companion object {
        private val currentDataSourceName = NamedThreadLocal<String>("")

        fun getCurrentDataSourceName(): String {
            return currentDataSourceName.get()
        }

        fun setCurrentDataSourceName(name: String) {
            currentDataSourceName.set(name)
        }
        fun removeCurrentDataSourceName() {
            currentDataSourceName.remove()
        }
    }
}
```




### 2. SpringBoot2 에서 JPA 에 multiple datasources 연결하기
spring-boot-starter-data-jpa 는 application run 시점에 entityManager 에서 autoConfiguration 을 실행한다.
hibernate-entityManager 를 포함하고 있지만 DataSource 별 Entity, Repository Scan 을 위해 entityManager와 transactionManager 를 설정해준다.

```
class JpaConfiguration {

    fun entityManager(dataSource: DataSource, packageToScan: String): LocalContainerEntityManagerFactoryBean {
        val em = LocalContainerEntityManagerFactoryBean()
        em.setPackagesToScan(packageToScan)
        em.dataSource = dataSource

        // jpaVendorAdapter 를 설정은 생략할 경우 persistence properties 가 설정되지 않아 bean 생성시 exception 발생
        em.jpaVendorAdapter = HibernateJpaVendorAdapter()

        return em
    }

    fun transactionManager(entityManager: LocalContainerEntityManagerFactoryBean): JpaTransactionManager {
        return JpaTransactionManager(entityManager.`object`!!)
    }
}
```


### 3. AOP 를 이용한 DataSource 라우팅
AOP 에서 데이터소스 라우팅 시점에 dataSourceContext 클래스의 NamedThreadLocal 프로퍼티 값을 현재 사용할 데이터소스명으로 변경해준다.
AbstractRoutingDataSource 는 determineCurrentLookupKey 를 수행하여 targetDataSources 맵에서 변경된 데이터소스명을 키로 가지는 DataSource를 라우팅한다.
```
RoutingDataSourceContext.setCurrentDataSourceName(dataSourceName)


try {
    return joinPoint.proceed()
} finally {
    RoutingDataSourceContext.removeCurrentDataSourceName()
}

```
