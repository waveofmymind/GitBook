---
description: 그리디
---

# Level 2. 호텔 대실

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/155651" %}

## 접근

그리디 문제로, 예전에 해결했던 백준의 [강의실 배정](https://waveofmymind.gitbook.io/waveofmymind/algorithms/undefined/11000.) 문제와 동일하다.

대신 주어진 시간을 비교하기 편하도록 별도로 계산할 필요가 있을 것 같다.

## 풀이

```python
import heapq

def solution(book_time):
    arr = []
    for bt in book_time:
        m,s = map(int,bt[0].split(":"))
        mm,ss = map(int,bt[1].split(":"))
        arr.append((m * 60 + s, mm * 60 + ss))
    arr.sort()
    hq = []
    
    hq = [0]
    for start, end in arr:
        can_use = heapq.heappop(hq)
        
        if can_use + 10 <= start:
            heapq.heappush(hq,end)
        else:
            heapq.heappush(hq,can_use)
            heapq.heappush(hq,end)
    return len(hq)
```

24시간제로 시간이 주어지기 때문에, 나는 정렬하기 편하도록 모든 시간을 분으로 나타내었다.

이제 보니, 그냥 문자열 자체에서 정수화 하더라도 비교할 수 있을 것이다.

그리고 시작시간으로 스케줄을 정렬해준다.

그리고 우선 순위 큐로 사용할 hq 배열은 초기 객실은 최소 하나이므로 \[0]으로 초기화한다.

그리고 우선 순위 큐에는 현재 예약이 끝나는 시간을 넣어놓을 것이기 때문에, 매번 can\_use 변수와 현재 start 시간과 비교해서 start가 크거나 같을 경우 현재 객실에 이어서 예약이 가능하다.

그러나 주의할 점은, 청소시간이 10분 소요되기 때문에 can\_use + 10과 비교해주어야한다.

만약 사용 불가능하면, 새로운 객실에 배치해야하는데, 이때 can\_use는 사용하지 않았기 때문에 다시 힙큐에 넣고, 새로운 객실은 end에 끝나기 때문에 end 값도 힙큐에 넣어준다.

