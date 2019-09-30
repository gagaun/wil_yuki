## Kapt

코틀린에서 JPA를 사용하기위해서 build를 추가해도, 바로 동작하지 않는데 이때 kapt 컴파일러 플러그인을 함께 사용해줘야 한다.
Dagger 나 JPA 같은 Annotation processor 라이브러리를 코틀린에서 사용하기 위해서다.

##### kotlin-kapt 플러그인 적용 (Gradle)
```
plugins {
    kotlin("kapt") version "1.3.21"
}
```

이런 간단한 syntax도 지원한다.
```
apply plugin: 'kotlin-kapt'
```
