# Level 2. 기능개발

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/42586" %}

## 접근

현재 작업이 100%가 되었더라도, 이전 작업이 아직 100%가 아닐 경우 이전 작업이 완료될 때 같이 배포되는 것을 명심해야한다.

요즘 많이 사용하던 것인, 특정 조건을 만족했을 때까지 pop하는 반복문을 활용해야할 것 같다.

## 풀이

```python
from collections import deque
def solution(progresses, speeds):
    
    progresses = deque(progresses)
    speeds = deque(speeds)
    answer = []
    
    while progresses:
        cnt = 0
        for i in range(len(progresses)):
            progresses[i] += speeds[i]
            
        while progresses and progresses[0] >= 100:
            cnt += 1
            progresses.popleft()
            speeds.popleft()
            
        if cnt:
            answer.append(cnt)
    return answer
            
                
        
        
            
```

우선 progresses와 speeds를 모두 큐로 만들어준다.

이유는 이전 작업을 먼저 판단해야하기 때문에 항상 0번째 인덱스부터 확인해야하기 때문이다. pop(0)은 O(N), popleft()는 O(1)의 시간복잡도를 갖는다.

progresses에 원소가 존재할 때까지 반복문을 돌고,

cnt는 그 날 배포할 수 있는 기능 갯수이다.

그 날 할당치를 추가하기 위해 for문을 수행하며 progresses\[i]에 speeds\[i]를 더해준다.



그 다음이 핵심인데, 만약 progresses에 원소가 존재하고 가장 이전 작업이 배포가 가능할 때까지, 새로운 반복문을 수행한다.

만약에 1번 TC처럼 첫번째가 7일, 두번째가 3일 소요되면 두번째 기능이 100이 되더라도 progresses\[0]을 기준으로 반복문에 진입하기 때문에 첫번째 기능(이전 기능)이 100이 되지 않으면 cnt를 증가할 수 없다.

만약 가장 이전 기능이 100이 되면 그때 배포할 수 있는 progresses가 있는지 확인하기 위해 반복문을 돌면서 cnt를 증가한다.



배포할 수 있는 기능을 모두 추려내면 cnt는 최소 1 이상이므로&#x20;

배포할 수 있는 것이 있으면(if cnt) 정답 배열에 추가한다.





