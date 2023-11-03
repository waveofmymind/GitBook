# Level 2. 전화번호 목록

{% embed url="https://school.programmers.co.kr/learn/courses/30/lessons/42577?language=python3" %}

## 접근

A가 B의 접두사라는 것은 B의 첫부분이 A일때이다.

즉, '010'은 '01012345678'의 접두사이다.

그렇기 때문에 내림차순 정렬을 하면, 접두사끼리 정렬되기 때문에

자기 자신의 다음 번호와 비교하면 된다.

## 풀이

```python
def solution(phone_book):
    phone_book.sort()
    for i in range(len(phone_book)-1):
        target_len = len(phone_book[i])
        if phone_book[i] == phone_book[i+1][:target_len]:
            return False
    else:
        return True
```

phone\_book\[i+1]은 자기 다음 번호의 문자열이고, 슬라이싱을 이용해서 target\_len까지의 값만 phone\_book\[i]로 비교를 해서 같을 경우 False를 반환한다.

만약 반복문을 다 돌았다면 어떤 번호도 접두어가 되지 못했다는 의미이므로 True를 리턴한다.
