## 이것이 코딩테스트다 with 파이썬_알고리즘
- 그리디 알고리즘 예시 중, "1이 될 때까지" 출력 조건이 **첫째 줄에 N이 1이 될 때까지 1번 혹은 2번의 과정을 수행해야 하는 횟수의 최솟값을 출력**이라면, (1번 : N에서 1을 뺀다 / 2번 : N을 K로 나눈다)   
  101p 예시에서 추가로 수정해야 된다.

```python
n, k = map(int, input().split())
result1 = 0
result2 = 0

# N이 K 이상이라면 K로 계속 나누기
while n >= k:
  # N이 K로 나누어 떨어지지 않는다면 N에서 1씩 빼기
  while n % k != 0:
    n -= 1
    result1 += 1
  # K로 나누기
  n //= k
  result2 += 1

# 마지막으로 남은 수에 대하여 1씩 빼기
while n > 1:
  n -= 1
  result1 += 1

print(min(result1, result2))
```

- 이렇게 하면 1번 방법과 2번 방법의 수행 횟수를 따로 관리해서 그 중 최솟값을 출력할 수 있다.
