---
description: DFS
---

# Level 2. 타겟 넘버

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/43165" %}

## 접근

numbers의 원소들을 모두 사용해서 target을 만들 수 있는 경우의 수를 찾는다.

핵심은, numbers의 원소들을 사용할 때, 빼거나 더하기만 가능하다.

## 풀이

```python
def solution(numbers, target):
    answer = 0
    def dfs(depth,total):
        if depth == len(numbers):
            if target == total:
                nonlocal answer
                answer += 1
            return
        dfs(depth + 1, total + numbers[depth])
        dfs(depth + 1, total - numbers[depth])  
    dfs(0,0)
    return answer
```

dfs() 함수를 정의해서 해결했다.

인자로는 numbers의 인덱스로 사용할 depth와 합을 나타내는 total이 있다.

만약 depth가 numbers의 길이와 같아지면 target과 total을 비교하는데, numbers의 길이와 같아질 경우 numbers 배열의 끝까지 도달했다는 것이기 때문이다.

만약 아직 더 나아갈 수 있을 경우(depth < len(numbers) 재귀적으로 함수를 호출한다.

이때 핵심은 depth는 무조건 +1을 해주어야하고, total에 numbers\[depth]를 더하거나, 빼거나로 무조건 처리해야한다는 점이다.

만약 dfs(depth + 1, total)의 경우, 해당 depth의 numbers 원소를 사용하지 않겠다는 의미이기 때문이다.

