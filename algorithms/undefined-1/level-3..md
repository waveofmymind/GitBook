---
description: 해시
---

# Level 3. 베스트앨범

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/42579" %}

## 접근

노래를 넣는 기준은 1. 장르별 음악의 총 재생 횟수, 2. 각 음악당 재생 횟수 3. 숫자가 낮은 순서이다.

## 풀이

```python
from collections import defaultdict

def solution(genres, plays):
    answer = []
    genre_count = defaultdict(int)
    arr = [(i,v,plays[i]) for i,v in enumerate(genres)]
    for i in range(len(genres)):
        genre_count[genres[i]] += plays[i]
        
    arr.sort(reverse=True, key = lambda x : (genre_count[x[1]], x[2], -x[0]))
    insert = defaultdict(int)
    for a in arr:
        if insert[a[1]] == 2:
            continue
        else:
            answer.append(a[0])
            insert[a[1]] += 1
    return answer
            
```

genre\_count 딕셔너리에 장르별 음악 재생 횟수의 합을 구한다.

그리고 arr배열은 원소가 (음악의 고유번호, 장르, 재생 횟수)로 구성되어있기 때문에 아까 정의한 순서대로 정렬을 해준다.

이때 고유 번호는 작은 것이 우선시되어야하기 때문에 -를 이용해서 혼자서만 오름차순 정렬을 해주었다.

그리고 insert 딕셔너리는 각 key가 장르로, 몇번 들어가있는지 체크하기 위해 만들었다.

만약 insert\[key] 가 2이면 continue한다.
