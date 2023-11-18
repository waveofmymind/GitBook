# 베스트 앨범

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/42579" %}

## 접근

정렬 조건을 잘 기억하자.

장르 재생횟수 순 (내림차)

트랙 별 재생횟수 순(내림차)

인덱스 (오름차)

그리고 장르 별 조건에 맞는 것 2개까지만 묶어서이다.

## 풀이

```kotlin
class Solution {
    data class Music(val id: Int, val play:Int, val gen: String) 
    fun solution(genres: Array<String>, plays: IntArray): IntArray {
        val maps = mutableMapOf<String,Int>()
        
        genres.forEachIndexed {
            index,gen -> 
            maps[gen] = maps.getOrDefault(gen, 0) + plays[index]
        }
        
        var arr = genres.mapIndexed {
            index,gen -> Music(index,plays[index]!!,gen)
        }
        
        val answer = arr.sortedWith(Comparator {
            m1,m2 -> 
            if (m1.gen == m2.gen) {
                if (m1.play == m2.play) {
                    m1.id - m2.id
                } else {
                    m2.play - m1.play
                }
            } else {
                maps[m2.gen]!! - maps[m1.gen]!!
            }
        })
        
        return answer.groupBy {it.gen}.map {
            it.value.take(2)
        }.flatten().map { it.id }.toIntArray()
    }
}
```

코드가 길어 파트별로 설명해보면,

### 1. 장르 별 총 재생횟수 카운팅

genres 배열에는 장르가 담겨있고, 각 장르의 인덱스로 plays 배열 원소의 위치가 해당 곡의 재생 횟수이다.

그렇기 때문에 maps에 <장르 이름, 재생횟수 합>으로 카운팅 해주었다.

이는 추후 정렬을 위해서이다.

```kotlin
genres.forEachIndexed {
            index,gen -> 
            maps[gen] = maps.getOrDefault(gen, 0) + plays[index]
        }
```

forEachIndexed로 scope를 열면, 첫번째 인자가 인덱스, 두번째 인자가 밸뷰이다.

### 2. Music 데이터 클래스

노래별로 장르, 인덱스, 재생횟수를 담기 위해서 만든 클래스이다.

genres 배열을 이용해서 클래스 배열을 만들 수 있다.

```kotlin
 data class Music(val id: Int, val play:Int, val gen: String) 
 
 var arr = genres.mapIndexed {
            index,gen -> Music(index,plays[index]!!,gen)
        }
        
```

forEachIndexed와 마찬가지로, mapIndexed를 하면 인덱스와 밸류 모두를 이용할 수 있다.

### 3. 정렬

복잡해보이지만 별거 없다.

우선 접근때 얘기했던 순서를 다시 복기해보자.

1. 장르의 총 재생횟수가 많은 것들 내림차
2. 장르가 같을 경우, 노래 별 재생횟수가 많은 것들 내림차
3. 장르, 재생횟수가 모두 같을 경우 인덱스로 오름차

```kotlin
val answer = arr.sortedWith(Comparator {
            m1,m2 -> 
            if (m1.gen == m2.gen) {
                if (m1.play == m2.play) {
                    m1.id - m2.id
                } else {
                    m2.play - m1.play
                }
            } else {
                maps[m2.gen]!! - maps[m1.gen]!!
            }
        })
```

sortedWith을 이용하면 반환값으로 정렬된 배열을 새로 받을 수 있고, 인자로 Comparator를 넘기면 커스텀한 정렬 기준을 만들 수 있다.

m1, m2는 각각 Music 클래스이다.

첫번째 if는 장르가 같을 경우, 두번째 if는 노래별 재생횟수가 같을 경우, 마지막으로 인덱스를 기준으로 오름차 정렬한다.

m1,m2에 대해서 첫번째 인자가 앞이면(m1-m2) 오름차, m2-m1은 내림차이다.

### return

```kotlin
return answer.groupBy {it.gen}.map {
            it.value.take(2)
        }.flatten().map { it.id }.toIntArray(
```

우선 Music클래스들을 장르별로 groupBy로 맵을 만들어준다.

이때 맵 타입은 <장르 명, 뮤직 리스트> 이므로 \<String, List\<Music>이다.

그리고 맵의 value(List\<Music)에 대해서 take(2)를 하면 상위 2개 Music을 뽑아 List로 만든다. 이렇게 하면 List\<List\<Music>>이 되서, 안쪽의 List\<Music>은 2개,2개일 것이다.

그리고 이를 flatten()을 하면 List\<Music>의 단일 리스트로 만들어준다.

그리고 이 음악 4개에 대해 인덱스로 정수 배열을 만든다.



## 복습

### 11월 18일

```kotlin
class Solution {
    fun solution(genres: Array<String>, plays: IntArray): IntArray {
        val totalPlayMap = mutableMapOf<String, Int>()
        
        genres.forEachIndexed { i, v ->
            totalPlayMap[v] = totalPlayMap.getOrDefault(v,0) + plays[i]!!
        }
        var arr = (0 until plays.size).toMutableList()
        arr.sortWith {
            a,b -> 
            if (totalPlayMap[genres[a]!!]!! == totalPlayMap[genres[b]!!]!!) {
                if (plays[a]!! == plays[b]!!) {
                    a - b
                } else {
                    plays[b]!! - plays[a]!!
                }
            } else {
                totalPlayMap[genres[b]!!]!! - totalPlayMap[genres[a]!!]!!
            }
        }
        return arr.groupBy { genres[it] }.map {
            it.value.take(2)
        }.flatten().toIntArray()
    }
}
```

생각해보니 굳이 Music 클래스로 인덱스와 재생횟수를 들고 있을 필요가 없다고 생각해서 genres.size 만큼의 정수 배열을 이용했다.

그러나 코틀린에서는 맵에 key값을 접근할 때 널 아님 단언을 !!로 선언해주어야해서, 정렬 로직에서 좀 더러운 것 같다.
