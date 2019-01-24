

`kotlin을 서비스코드에 사용하면서 자주 검색하게 되는 코틀린 basic 문법을 정리해본다 ^ㅠ^`



## Idioms
document :
https://kotlinlang.org/docs/reference/idioms.html

#### Instance Checks
```kotlin
when(x) {
  is Foo -> ...
  is Bar -> ...
  else -> ...
}
```
* is 는 smart casting을 지원.
* when 구문으로 instance check시 else 를 명시하지 않으면 ide 에서 경고.


#### if not null
```
val len = foo?.size
```

#### if not null and else
```
val len = foo?.size ?: 0
```

#### execute a statement if null
```
val len = values["email"] ?: throw IllegalStateException()
```

#### execut if not null
```
value?.let {
  ... // if not null
}
```
* let 구문 다음에 ?: 연산자를 사용하여 else 처리를 할 수 있는데, statement 가 아닌 block 이 와야 하는 경우 run 키워드를 함께 사용할 수 있다.

```
?: run {
  ... // else
}
```


#### with - calling muitiple methods
```
class Foo {
  fun a()
  fun b()
  fun c(idx: Int)
}
val foo = Foo()
with(foo {
  a()
  for (i in 1..3) {
    c(i)
  }
  b()
}
```

#### nullable Boolean
```
var b: Boolean? = ...
if (b == true) {
  ...
} else {
  ... // b is false or null
}
```
* nullable boolean 인 경우 if 만 사용했을 경우 ide에서 warning 이 발생한다.
