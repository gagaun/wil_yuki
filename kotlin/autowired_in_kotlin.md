## Springboot @Autowired in kotlin


##### 참고자료
https://www.baeldung.com/spring-autowire
https://kotlinlang.org/docs/reference/properties.html#late-initialized-properties

----


Spring Framework 는 2.5 버전부터  @Autowired 어노테이션을 통한 새로운 DI 방법을 도입했다. Kotlin 에서는 BeanDsl 을 통한 DI 를 제공하지만, 어노테이션을 사용하는 경우 프로퍼티와 생성자 레벨에서 선언 할 수 있다.


### @Autowired on Properties
#### Java
```
@Component
public class FooService {

    @Autowired
    private FooFormatter fooFormatter;

}
```


#### Kotlin
```
@Component
class FooService {

    @Autowired
    private lateinit var fooFormatter: FooFormatter
}
```


Non-null 타입 프로퍼티는 생성자에서 반드시 초기화 되어야하는데, DI 를 통한 초기화등 지연이 필요한 경우 lateinit 키워드를 사용한다. lateinit 을 통해 선언된 변수가 초기화 되었는지는 isInitialized 함수를 통해 확인할 수 있다.



### @Autowired on Constructor
#### Java
```
public class FooService {

    private FooFormatter fooFormatter;


    @Autowired
    public FooService(FooFormatter fooFormatter) {
        this.fooFormatter = fooFormatter;
    }
}
```

#### Kotlin
```
class FooService @Autowired constructor(private val fooFormatter)
```
@Autowired constructor 를 통해 쉽게 생성자를 선언할 수 있다. Spring4.2 이후 다음과 같이 생략도 가능하다.
```
class FooService(private val fooFormatter)
```
