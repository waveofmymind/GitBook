# 🤝 Coroutine

## Co-routine

> 협력하는 루틴

### 일반적인 루틴

```kotlin
// 루틴
fun main() {
    println("START")
    newRoutine()
    println("END")
}

fun newRoutine() {
    val num1 = 1
    val num2 = 2
    println("${num1 + num2}")
}
```

위 루틴은 START 출력 - newRoutine() 실행 - END 출력으로 끝나는 직관적인 실행 과정을 띈다.

즉, 아래와 같다.

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p>일반적ㅣ ㅇㅇㅇ</p></figcaption></figure>

newRoutine()이 실행되면 스택에 num1,num2 지역 변수를 할당하고, 루틴이 끝나면 GC에 의해 미래에는 삭제될 것이다.

### 코-루틴

```kotlin
fun main(): Unit = runBlocking {
    println("START")
    launch {
        newRoutine()
    }
    yield()
    println("END
}

suspend fun newRoutine() {
    val num1 = 1
    val num2 = 2
    yield()
    println("${num1 + num2})"
}

// [main] - START
// [main] - END
// [main] - 3
```

**runBlocking**: 새로운 코루틴을 만들며, 일반 루틴과 코루틴 세계를 연결한다.

**launch**: 반환값이 없는 코루틴을 만든다.

**suspend fun**: 다른 suspend fun을 호출할 수 있다.

**yield**: 현재 코루틴을 중단하고, 스레드가 다른 코루틴을 실행하도록 한다.

즉 커스텀한 CoroutineScope를 생성하고, 안에서 launch로 새로운 코루틴을 만든다.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

중단되었다가 재개하더라도 다시 수행할 수 있어야하기때문에 정보를 보관해야한다.

**루틴이 중단되었다가 나중에라도 해당 메모리에 접근이 가능해야한다.**

현재 실행한 라인의 코루틴을 확인하는 VM 옵션

> \-Dkotlinx.coroutines.debug

## 정리

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

코루틴의 경우 코루틴이 중지되더라도 추후 메모리 접근이 가능해야한다.
