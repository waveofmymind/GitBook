# CoroutineScope와 CoroutineContext

Coroutine 내에서 새로운 root 코루틴을 만들때 CoroutineScope를 사용해서 생성할 수 있다.

그리고 launch와 async는 CoroutineScope의 확장 함수이다.

그렇기 때문에, runBlocking이 CoroutineScope을 제공하기 때문에 여태 launch와 async를 사용할 수 있었던 것이다.

만약, 직접 CoroutineScope를 만들면 runBlocking은 필요하지 않다.

```kotlin
fun main() {
    CoroutineScope(Dispatchers.Default).launch {
        delay(1_000L)
        printWithThread("Job 1")
    }
    Thread.sleep(1_500L) // or launch를 변수로 받아 join()
}
```

### CoroutineScope의 주요 역할

> CoroutineContext에 데이터를 보관한다.

CoroutineContext에는 여러가지 데이터를 가지고 있다.(코루틴 이름, ExceptionHandler 등등)

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

자식 코루틴을 생성할 때, 부모 코루틴의 Context를 복사해 필요한 내용을 덮어 쓴다.

이때 자식-부모 관계도 설정한다.

## CoroutineContext

Map + Set을 합쳐놓은 상태

## CoroutineDispatcher

코루틴을 스레드에 배정하는 역할을 한다.

### Dispatchers.Default

CPU 자원을 많이 쓸 때 권장

### Dispatchers.IO

I/O 작업을 할 때 권장

### Dispatchers.Main

UI 작업을 할 때 사용된다.

### 자바의 threadPool.asCoroutineDispatcher()
