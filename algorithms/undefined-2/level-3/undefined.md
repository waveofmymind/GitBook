# 이중 우선 순위 큐

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/42628" %}

## 접근

최대 큐와 최소 큐를 만들어서 넣을 때에는 둘 다 넣고, 뺄 때만 주의하면 된다.

## 풀이

```kotlin
import java.util.*

class Solution {
    
    fun solution(operations: Array<String>): IntArray {
        val maxQ = PriorityQueue<Int>(Collections.reverseOrder())
        val minQ = PriorityQueue<Int>()
        operations.forEach {
            operation ->
            
            val (com, num) = operation.split(" ")
            
            when(com) {
                "I" -> {
                    maxQ.offer(num.toInt())
                    minQ.offer(num.toInt())
                }
                "D" -> {
                    when(num) {
                        "-1" -> {
                            minQ.poll()?.let { maxQ.remove(it) }
                        }
                        "1" -> {
                            maxQ.poll()?.let{ minQ.remove(it) }
                        }
                    }
                }
            }
        }
        if(maxQ.isNotEmpty()) {
            val max = maxQ.peek()
            val min = minQ.peek()
            return intArrayOf(max,min)
        }
        return intArrayOf(0,0)
    }
}
```

우선순위큐의 디폴트는 최소 힙이기 때문에, 최대 힙을 만들기 위해서는 인자로 Collections.reverseOrder()을 넘기면 된다. 기억하자.

그리고 forEach를 이용해서 명령어들을 순회한다.

operation을 공백으로 나누면, 문자열과 숫자로 나뉘는데, 문자가 명령, 숫자가 값이나 조건이다.

명령이 I일 때에는 최소힙 최대힙 두 힙에 값을 넣어주고,

D일 때를 유심히 보자.

\-1은 최솟값을 제거하는 것이므로 최소 힙에서 값 하나를 제거해야한다.

그러나 문제 조건에 빈 큐에 대한 명령어는 무시하라고 되어있기 때문에,

poll()은 빈 큐에 실행할 경우 Null을 반환하기 때문에 세이프티 호출을 이용해서 빈 큐가 아닐 경우에만 let 로직을 수행한다.

