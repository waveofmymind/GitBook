# 코루틴의 예외 처리 및 Job의 상태 변화

## launch

```kotlin
fun main(): Unit = runBlocking {
    val job = CoroutineScope(Dispatchers.Default).launch {
        throw IllegalArgumentException()
    }
    delay(1_000L)
}
```

launch로 생성한 코루틴은 예외가 발생할 경우 예외를 출력하고 코루틴이 종료된다.

## async

```kotlin
fun main(): Unit = runBlocking {
    val job = CoroutineScope(Dispatchers.Default).async {
        throw IllegalArgumentException()
    }
    delay(1_000L)
    job.await() // 예외를 확인하려면 출력한다.
}
```

그러나 async의 경우 예외가 발생해도 출력되지 않고 종료 된다.

async에서 생성된 예외를 발생시키려면, job.await()를 사용한다.

## 예외 전파

<pre class="language-kotlin"><code class="lang-kotlin">fun main(): Unit = runBlocking {
    val job = async {
        throw IllegalArgumentException()
    }
    delay(1_000L)
<strong>}
</strong></code></pre>

위처럼 루트 코루틴을 따로 두지않고, 자식 코루틴에서 예외를 발생시키면 async를 사용했음에도 바로 코루틴이 종료된다.

이는 자식 코루틴의 예외가 부모 코루틴으로 전파되었기 때문이다.

## 예외를 전파시키고 싶지 않다면?

### Supervisor Job을 사용한다.

```kotlin
fun main(): Unit = runBlocking {
    val job = async(SupervisorJob()) {
        throw IllegalArgumentException()
    }
    delay(1_000L)
}
```

부모-자식의 관계는 지속되지만 예외가 부모로 전파되지 않는다.

자식의 예외를 확인하려면 마찬가지로 await()를 사용한다.

### try-catch

```kotlin
fun main(): Unit = runBlocking {
    val job = launch {
      try {
        throw IllegalArgumentException()
      } catch (e: IllegalArgumentException) {
        printWithThread("정상 종료")
      }
    }
    delay(1_000L)
}
```

### CoroutineExceptionHandler

```kotlin
fun main(): Unit = runBlocking {
    val exceptionHandler = CoroutineExceptionHander { _, _ -> 
        printWithThread("예외")
    }
    
    val job = CoroutineScope(Dispatchers.Default).launch(exceptionHandler) {
        throw IllegalArgumentException()
    }
    delay(1_000L)
}
```

### 코루틴의 예외 정리

CancellationException일 경우, 취소로 간주하고 부모에게 전파 X

그 외의 예외일 경우 부모에게 전파 O
