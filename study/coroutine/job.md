# 코루틴 빌더와 Job

새로운 코루틴을 만들고, 루틴 세계와 코루틴 세계를 이어주는 역할을 하는 함수를 **코루틴 빌더**라고 한다.

## runBlocking

```kotlin
fun main() {
    runBlocking {
      printWithThread("START")
      launch {
        delay(2_000L) // yield()
        printWithThread("LAUNCH END")
      }
      // 딜레이되더라도 아래 "END" 출력 코드로 쓰레드가 넘어오지 않는다.
    printWithThread("END")
}

// [main] START
// [main] LAUNCH END
// [main] END
```

runBlocking은 runBlocking 내의 코루틴이 모두 수행될 때 까지 해당 스레드를 블로킹한다.

## launch

코루틴을 제어할 수 있는 객체 Job을 반환받는다.

여기서 제어란, 코루틴을 시작/취소/종료까지 대기할 수 있다는 의미이다.

```kotlin
fun main() {
    launch() // 빌드 에러, 바로 사용할 수 없다.
}

fun main(): Unit = runBlocking {
    val job = launch(start = CoroutineStart.LAZY) {
        printWithThread("Hello launch")
    }
    
    job.start() // start()를 선언하지 않으면 위 코루틴(launch)은 실행될 수 없다.    
}

fun main(): Unit = runBlocking {
    val job = launch {
        (1..5).forEach {
            printWithThread(it)
            delay(500)
        }
    }
    
    delay(1_000)
    job.cancel()
}

// 500ms 간격으로 1씩 더하다가 1초 후에 cancel된다. 

// [main @coroutine#2] 1
// [main @coroutine#2] 2

fun main(): Unit = runBlocking {
    val job1 = launch {
        delay(1_000L)
        printWithThread("Job 1")
    }
    val job2 = launch {
        delay(1_000L)
        printWithThread("Job 2")
    }
}
// job1이 실행되고 1초를 기다렸다 job2를 실행하는 것이 아니라, job1이 delay되고 바로 job2를 실행한다.

```

즉, 대기의 경우 아래와 같다.

&#x20;

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

### join()

위에서 delay 순간에 다른 코루틴이 수행되는 것을 막으려면 대기시키려는 job.join()을 하면 된다.

```kotlin
fun main(): Unit = runBlocking {
    val job1 = launch {
        delay(1_000L)
        printWithThread("Job 1")
    }
    job1.join() 
    val job2 = launch {
        delay(1_000L)
        printWithThread("Job 2")
    }
}
```

즉 아래와 같이 수행된다.

&#x20;

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### launch 정리

1. start(): 시작 신호
2. cancel(): 취소 신호
3. join(): 코루틴이 완료될 때 까지 대기

## async

코루틴 내에서 실행한 함수의 결과를 반환할 수 있다.

```kotlin
fun main(): Unit = runBlocking {
    val job = async {
        3 + 5
    }
    val eight = job.await()
}
```

이때 변수 job의 타입은 8인 Int가 아니고, Deferred이다.

### Deffered

Job을 상속받고 있으며, await()를 사용할 수 있다.

await()은 async의 결과를 가져오는 함수이다

> 여러 API를 동시에 호출하여 소요시간을 최소화 할 수 있다.

```kotlin
fun main(): Unit = runBlocking {
    val job1 = async { apiCall1() }
    val job2 = async { apiCall2() }
    printWithThread(job1.await() + job2.await())
}

suspend fun apiCall1(): Int {
    delay(1_000L)
    return 1
}

suspend fun apiCall2(): Int {
    delay(1_000L)
    return 2
}

// 수행시간 약 1.1초 정도
```

콜백을 이용하지 않고 동기 방식으로 비동기 코드를 작성할 수 있다.

```kotlin
fun main(): Unit = runBlocking {
    val job1 = async { apiCall1() }
    val job2 = async { apiCall2(job1.await()) }
    printWithThread(job2.await())
}

suspend fun apiCall1(): Int {
    delay(1_000L)
    return 1
}

suspend fun apiCall2(num: Int): Int {
    delay(1_000L)
    return num + 2
}
```

위처럼 작성하면, indent 증가 없이 손쉽게 작성할 수 있다.
