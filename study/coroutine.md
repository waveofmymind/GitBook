# 🤝 Coroutine

## Co-routine

> 협력하는 루틴

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

위 루틴은, START - newRoutine() - END로 끝나는 직관적인 실행 과정을 띈다.

즉, 아래와 같다.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption><p>일반ㄱㅣㅇㅇ</p></figcaption></figure>
