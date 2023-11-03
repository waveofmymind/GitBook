# Level 3. 입국 심사

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/43238" %}

## 접근

이분 탐색에서 가장 중요한 것은 lt와 rt를 어떻게 잡을 지와 mid가 정답 조건에 맞는지를 체크하는 것이다.

## 풀이

```python
def solution(n, times):
    lt = 1
    rt = max(times) * n
    answer = 0
    while lt <= rt:
        mid = (lt + rt) // 2
        
        people = 0
        
        for time in times:
            people += mid // time
            
            if people >= n:
                break
        if people >= n:
            rt = mid - 1
            answer = mid
        else:
            lt = mid + 1
    return answer
```

최솟값과 최댓값을 모든 사람이 상담 받는 시간으로 삼는다.

즉, lt는 1, rt는 모든 사람이 가장 오래 걸리는 상담 시간으로 한다.

그리고 for time in times부터 설명하면,

mid // time은 각 상담원들이 mid동안 상담할 수 있는 인원이다.

만약 people이 n보다 크거나 같은 경우 탈출한다.
