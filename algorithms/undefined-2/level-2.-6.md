# Level 2. 주식가격

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/42584?language=python3" %}

## 접근

괄호 체크하는 문제처럼 스택을 이용하는 전형적인 대표 문제인 것 같다.

리트코드에서 봤던 날씨가 떨어지지 않는 날을 구하는 [문제](https://leetcode.com/problems/daily-temperatures/description/)와 유사하다.

for문으로 prices를 순회하면서 현재 price를 스택에 값과 비교한다.

## 풀이

```
def solution(prices):
    stack = []
    answer = [0] * len(prices)
    for idx, price in enumerate(prices):
        if not stack:
            stack.append((idx, price))
        else:
            while stack and stack[-1][1] > price:
                day,p = stack.pop()
                answer[day] = idx-day
            stack.append((idx,price))
    while stack:
        day, price = stack.pop()
        answer[d] = len(prices) - day - 1
    return answer
```

흐름대로 문제를 해석해보면,

스택이 비었을 경우 일수 체크를 해야하기 때문에 인덱스, 값을 튜플로 스택에 넣는다.

이때 인덱스를 넣는 이유는, **얼마나 떨어지지 않았는지와 answer에 값을 넣을 때 인덱스 위치로 넣어야한다.**

스택에 값이 있을 경우 while문 조건을 체크하는데, 스택에 값이 있을 때와 스택의 top이 현재 price보다 크면, 값이 떨어진 것이므로 스택에서 제거해야한다.

while문을 탈출하면 남아있는 스택의 값들은 모두 현재 price보다 작아서 값이 아직 떨어지지 않은 것들이다.

그리고 현재 idx,price도 일수 체크를 해야하므로 stack에 넣어준다.

prices에 대한 반복문을 탈출하고, 스택에 남은 값들은 한번도 값이 떨어지지 않은 가격들이다.

그러므로 스택이 빌 때 까지 answer에 값을 추가해준다.
