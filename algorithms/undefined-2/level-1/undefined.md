# 성격 유형 검사하기

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/118666" %}

## 풀이

```kotlin
class Solution {
    
    fun calculate(maps: Map<Char,Int>):String {
        var buf = ""
        
        if (maps.getOrDefault('R', 0) >= maps.getOrDefault('T', 0)) {
            buf += 'R'
        } else {
            buf += 'T'
        }
        
        if (maps.getOrDefault('C', 0) >= maps.getOrDefault('F', 0)) {
            buf += 'C'
        } else {
            buf += 'F'
        }
        
        if (maps.getOrDefault('J', 0) >= maps.getOrDefault('M', 0)) {
            buf += 'J'
        } else {
            buf += 'M'
        }
        
        if (maps.getOrDefault('A', 0) >= maps.getOrDefault('N', 0)) {
            buf += 'A' 
        } else {
            buf += 'N'
        }
        return buf
    }
    
    fun solution(survey: Array<String>, choices: IntArray): String {
        val maps = mutableMapOf<Char, Int>()
        
        choices.forEachIndexed { index, value -> 
            if (value > 4) {
                val weight = value % 4
                maps[survey[index][1]] = maps.getOrDefault(survey[index][1], 0) + weight
            } else if (value < 4) {
                val weight = (4-value) % 4
                maps[survey[index][0]] = maps.getOrDefault(survey[index][0], 0) + weight
            }
        }
        
        val result = calculate(maps)
        return result
    }
}
```

성격 유형 검사를 자동화하기 위해서 %를 이용했다.
