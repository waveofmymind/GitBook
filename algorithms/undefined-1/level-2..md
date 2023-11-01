# Level 2. 의상

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/42578" %}

## 접근

각 파츠별로 한개의 의상만을 입거나, 아예 입지 않는 선택지가 존재한다.

예를 들어 1번 TC에서는 옐로우 햇과 그린 터반으로 hat에는 두가지 의상이 존재한다.

이때 햇을 선택하는 가짓수는&#x20;

1. 옐로우 햇을 착용한다.
2. 그린 터반을 착용한다.

단순히 보면 두가지라고 생각할 수 있지만, 해당 파츠를 아예 안입는 경우도 정답으로 포함해야한다.

그렇기 때문에 hat을 고르는 가짓수는 3가지이다.

각 파츠별로 곱하면, 3 \* 2 = 6이 나오는데, 이때 안 입은 가짓수도 포함해서 곱했기 때문에 두 파츠 다 안 입는 경우를 하나 빼줘야한다.

## 풀이

```python
from collections import defaultdict

def solution(clothes):
    closet = defaultdict(list)
    answer = 1
    for clo in clothes:
        closet[clo[1]].append(clo[0])
    for c in closet.values():
        answer *= len(c) + 1
    return answer - 1
```

옷을 파츠별로 분리하기 위해서 defaultdict(list)를 사용했다.

이는 만약 키가 없는 value에 접근할 경우 기본 값으로 만들어준다.

list의 경우 \[], int의 경우 0으로 만들어준다.

그리고 clo\[1]이라는 key로 clo\[0]을 value로 key에 대한 리스트에 넣어준다.

그리고 for문으로 values()에 대해서 가짓수 + 1 을 answer에 곱하고 -1 해주면 답이다.
