# Structured Concurrency

#### 코루틴의 LifeCycle

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

완료가 두 단계로 나누어져 있다.

자식 코루틴을 기다리기 때문이다.

```kotlin
fun main(): Unit = runBlocking {
    launch {
        delay(500L)
        printWithThread("A")
    }
    
    launch {
        delay(600L)
        throw IllegalArgumentException("코루틴 실패!")
}
```

위처럼 작성되어지면, A가 출력된 이후 예외가 발생한다.

```kotlin
fun main(): Unit = runBlocking {
    launch {
        delay(600L)
        printWithThread("A")
    }
    
    launch {
        delay(500L)
        throw IllegalArgumentException("코루틴 실패!")
}
```

그리고 위처럼 두 자식 코루틴 중 예외가 먼저 발생하면 부모에게 전파되고, 부모 코루틴은 다른 자식 코루틴에게 취소 요청을 보낸다.

자기 자신(부모)가 종료되어야하기 때문이다.

이처럼, 부모 - 자식이 한몸처럼 움직이는 것을 Structured Concurrency 라고 한다.

수많은 코루틴이 유실되지 않고, 에러가 적절히 보고 될 수 있음을 보장한다.
