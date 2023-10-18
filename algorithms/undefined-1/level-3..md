---
description: DFS
---

# Level 3. 네트워크

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/43162" %}

## 접근

네트워크 망임을 판단할 때, 탐색하지 않은 노드부터 최대한 끝까지 탐색해야한다.

그렇기 때문에 BFS보다 DFS로 해결하는 것이 적절한 것 같다.

## 풀이

```python
def solution(n, computers):
    graph = [[] for _ in range(n)]
    cnt = 0
    for i in range(n):
        for j in range(n):
            if i != j and computers[i][j] == 1:
                graph[i].append(j)
                
    visited = [False] * n
    
    def dfs(start):
        for next in graph[start]:
            nonlocal visited
            if not visited[next]:
                visited[next] = True
                dfs(next)
                
    for x in range(n):
        if not visited[x]:
            visited[x] = True
            dfs(x)
            cnt += 1
    return cnt
```

노드의 개수만큼 graph 배열을 List로 초기화한다.

graph\[i]는 i가 갈 수 있는 노드이다. 즉 간선이 연결된 것을 의미한다.

이중포문으로 computers 배열을 탐색하면서, i != j 이고 computers\[i]\[j] == 1인 경우에는 서로 다른 노드가 간선이 연결되어 있다는 것이므로 graph 배열에 추가한다.

각 노드는 하나의 네트워크에만 속할 수 있기 때문에 한번 탐색했던 노드는 패스하기 위해 visited로 방문 체크를 한다.
