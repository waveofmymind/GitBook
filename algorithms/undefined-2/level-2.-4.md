---
description: 그리디
---

# Level 2. 큰 수 만들기

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/42883" %}

## 접근

반복문을 돌면서, 현재 원소가 이전에 넣었던 값보다 클 경우 모두 빼고 새로운 값으로 갱신해야한다.

즉, Valid Palindrome 문제를 풀 때 처럼 한다.

## 풀이

```python
def solution(number, k):
    stack = []
    for n in number:
        while stack and stack[-1] < n and k > 0:
            stack.pop()
            k -= 1
        stack.append(n)
    if k != 0:
        stack = stack[:-k]
    return ''.join(stack)
```

number를 순회하면서, 반복문에 진입 조건을 설정한다.

k값이 0보다 클 때(수를 더 뺄 수 있을 때), 스택의 top이 n보다 클 때는 top을 빼고 k도 -1을 해준다.

만약 반복문을 다. 끝냈는데 k가 0이 아닌경우에는 더 빼야한다는 의미이다.

배열에서 마지막 k개만큼의 원소를 제거해준다.
