# Array Partition with Kotlin

{% embed url="https://leetcode.com/problems/array-partition/submissions/1093497126/" %}

## 접근

두 수의 조합 중 최소들의 합 중 최댓값을 구하는 것이다.

그렇다면 두 수의 조합의 최댓값은 어떻게 구할 수 있을까?

예시로 \[6,2,6,5,1,2]를 해보면,

5가 최솟값으로써 선택되려면 5 이상의 수와 비교되어야한다.

즉, 6 두개와 비교되면 된다.

안그러면 5는 선택되지 못하고 5보다 작은 수가 선택될 것이다.

그러나 배열 전체로 보면, 5가 단순히 자기보다 큰 수와 결합되는 것은 좋지 않다.

6이 5와 비교되는 것은 6 입장에서는 손해이기 때문이다.

그렇기때문에 최대한 자기 옆 수와 비교해야한다.

## 풀이

```kotlin
class Solution {
    fun arrayPairSum(nums: IntArray): Int {
        nums.sort()
        var answer = 0
        for (i in nums.indices step 2) {
            answer += minOf(nums[i],nums[i+1])
        }
        return answer
    }
}
```
