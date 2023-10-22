# Level 2. 구명 보트

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/42885" %}

## 접근

보트 하나에 최대한 사람을 많이 태워야 모든 사람을 태울 보트의 수를 최소화할 수 있다.

무게순으로 정렬해서 접근할 수 있을 것 같다.

## 풀이

```python
from collections import deque

def solution(people, limit):
    people.sort(reverse=True)
    q = deque(people)
    cnt = 0
    while len(q) > 1:
        now = q.popleft()
        
        if now + q[-1] <= limit:
            q.pop()
        cnt += 1
    if q:
        cnt += 1
    return cnt
```

우선 people 배열을 내림차순 정렬해주고, deque(people)로 q를 초기화한다.

그리고 q의 길이가 2 이상일 때까지 계속 반복문을 수행한다.

우선 가장 무거운 사람을 큐에서 빼준다.

그리고 보트에 추가로 태울 사람이 있는지 확인해야하는데, 보트는 최대 2명까지만 탈 수 있는 것을 생각하자.

가장 무거운 사람이 가장 가벼운 사람과 몸무게를 더해서 limit를 넘을 경우 그 가장 무거운 사람은 혼자밖에 탈 수 없다.

가장 가벼운 사람보다 더 가벼운 사람은 없기 때문이다.

그래서 now + q\[-1] <= limit인 경우 q\[-1]는 가장 가벼운 사람이고, 이 사람도 같이 태우기 위해서 pop()을 해준다.

그리고 반복문을 빠져나왔을 때에는 큐에 1이거나, 0일 때이므로, 큐가 1인 경우 마지막 한사람을 태워야하기 때문에 cnt를 1 더해서 반환한다.
