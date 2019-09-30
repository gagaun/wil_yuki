# 5.4 자바 함수형 인터페이스의 활용

코틀린 람다를 자바 API에 어떻게 활용할 수 있는지 알아보자.

**예제**

```java

public class Button {
  public void setOnClickListener(OnClickListener l) { ... }
}

public interface OnClickListener {
  void onClick(View v);
}

```

- onClick 메소드만 선언된 인터페이스 OnClickListener
- Button 의 메소드 setOnClickListener 는 이 인터페이스의 구현체를 인자로 받는다.

**자바 8 이전의 자바는 이를 위해 무명 클래스의 인스턴스를 만들어야만 했다.**

```java
button.setOnClickListener(new OnClickListener() {
  @Override
  public vod onClick(View v) {
   ...
  }
}
```

**코틀린에서는 무명 클래스 인스턴스 대신 람다를 넘길 수 있다**
```kotlin
button.setOnClickListener { view : View -> /** 클릭시 수행할 코드 동작 */ }
```

람다의 파라미터는 메소드의 파라미터와 대응한다. 인터페이스에 추상 메소드가 단 하나만 있기 때문이다.
- 이런 인터페이스를 **함수형 인터페이스** 또는 **SAM 인터페이스**라고 한다.

```java
public interface OnClickListener {
  void onClick(View v); // {view -> } 람다의 파라미터 view 는 java method 의 파라미터 view 에 대응한다.
}
```

## 5.4.1 자바 메소드에 람다를 인자로 전달
위처럼 함수형 인터페이스를 인자로 원하는 자바 메소드에 코틀린 람다를 전달할 수 있다.

```java
void postponeComputation(int delay, Runnable computation);
```
코틀린에서 이 함수에 람다를 넘기면 컴파일러가 자동으로 람다를 Runnable 인스턴스로 변환해준다.
```kotlin
postponeComputation(1000) { println(42) }
```
위 코틀린 람다를 무명 객체 구현으로 명시적으로 변경해보면 아래와같다
```java
postponeComputation(1000, object: Runnable {
  override fun run() {
    println(42)
  }
})
```

이 구현은 람다와 동일하게 동작할까? 람다와 무명 객체 사이의 차이점은 뭘까?
- 객체를 명시적으로 선언하면 메소드 호출시마다 **새로운 객체 생성**
- 람다는 람다에 대응하는 무명 객체를 메소드 호출할때마다 **반복 사용**


그래서 명시적이되 람다와 동일한 구현은 아래와 같다
```java
val runnable = Runnable { println(42) }
fun handleComputation() {
  postponeComputation(1000, runnable);
}
```
- runnable 은 전역변수로 컴파일된다. 프로그램 하나 단 하나의 인스턴스만 존재

---
p230
