# 코루틴의 취소

코루틴의 적절한 취소는 중요하다.

필요하지 않은 코루틴을 적절히 취소해 컴퓨터 자원을 아껴야 한다.

## delay

```kotlin
fun main(): Unit = runBlocking {
    val job1 = launch {
        delay(1_000L)
        printWithThread("Job 1")
    }
    
    val job2 = launch {
        delay(1_000L)
        printWithThread("Job 2")
    }
    
      delay(100)
      job1.cancel()  
}
[main @coroutine#3] Job 2
```

delay() / yield()를 호출하면, 코루틴이 취소를 협력하는 상태가 되어 cancel()이 가능하다.

그러나 다음과 같이 코드를 작성하면 취소되지 않는다.

```kotlin
fun main(): Unit = runBlocking {
    val job = launch {
        var i = 1
        var nextPrintTime = System.currentTimeMillis()
        while (i <= 5) {
            if (nextPrintTime <= System.currentTimeMillis()) {
                printWithThread("${i++}번째 출력!")
                nextPrintTime += 1_000L
            }
        }
    }
    
    delay(100L)
    job.cancel()
}
```

위 코드는 1초에 한번씩 출력하는 무한루프이지만, delay가 100L이므로 1만 출력되고 cancel되어야할 것으로 예상했다.

그러나 취소되지 않고 5까지 출력되는 것을 확인할 수 있다.

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

suspend 함수를 사용하지 않고, 쉬지 않고 출력하기 때문에 취소되지 않는 것이다.

## CancellationException

```kotlin
fun main(): Unit = runBlocking {
    val job = launch(Dispatchers.Default) {
        var i = 1
        var nextPrintTime = System.currentTimeMillis()
        while (i <= 5) {
            if (nextPrintTime <= System.currentTimeMillis()) {
                printWithThread("${i++}번째 출력!")
                nextPrintTime += 1_000L
            }
            
            if (!isActive) {
                throw CancellationException()
            }
        }
    }
    
    delay(100L)
    job.cancel()
}
```

* isActive: 현재 코루틴이 활성화 되어있는지, 취소 신호를 받았는지 확인한다.
* Dispatchers.Default: 우리의 코루틴을 다른 스레드에 배정한다.

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

사실 suspend 함수도 CancellationException을 사용해 코루틴을 취소시킨다.

```kotlin
fun amin(): Unit = runBlocking {
    val job = launch {
        try {
            delay(1_00L)
        } catch(e: CancellationException) {
            // 아무것도 안함
        } 
        printWithThread("delay에 의해 취소되지 않는다!")
    }
    delay(100L)
    printWithThread("취소 시작")
    job.cancel()
}
```
