# x만큼 간격이 있는 n개의 숫자

## 접근

x의 배수로 하여금 배열의 크기가 n개가 되도록 만들어주면 된다.

이때 코루틴의 배열 생성시 후행 람다를 사용할 수 있다.

## 풀이

```kotlin
class Solution {
    fun solution(x: Int, n: Int) = 
    LongArray(n) {
        x.toLong() * (it + 1)
    }
}
```

LongArray(n)으로 사이즈가 n이면서 각 원소의 타입은 Long인 배열을 생성한다.

이때 후행 람다에서 it은 인덱스이다.

주어진 요구사항을 2,5라고 했을때

0번째 인덱스가 2이므로 (it+1) \* x를 해주면 된다.
