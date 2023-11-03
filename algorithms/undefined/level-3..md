# Level 3. 성적 평균

{% embed url="https://softeer.ai/practice/6294" %}

## 접근

문제 자체는 간단하다.

주어진 구간으로 성적 리스트를 슬라이싱해서 원하는 구간의 배열의 합과 길이로 평균을 구한다.

그리고 round를 이용해서 반올림하고 출력한다.

## 풀이

```python
import sys

n, k = map(int,sys.stdin.readline().rstrip().split())
arr = list(map(int,sys.stdin.readline().rstrip().split()))
for _ in range(k):
  start,end = map(int,sys.stdin.readline().rstrip().split())
  target = round(sum(arr[start-1:end])/(end - start + 1),2)
  print('%0.2f' % target)
```

접근에서 했던 대로 해결 할 수 있었다.

그러나 예상치 못하게, 45.00인 경우 둘째자리에서 반올림 시켜도 45.0이 나오는 문제가 있었다.

그래서 이를 뒤집어서 0.45에서 문자열로 변환 -> zfill(5)로 앞에 0을 채워준 후, 다시 뒤집어서 float로 형 변환을 시도했지만, 45.00은 다시 45.0이 나왔다.

그래서 이를 포맷팅 함수라는 것을 이용해서 해결할 수 있었다.

0.2f의 경우 입력값을 둘째자리까지 채워준다.
