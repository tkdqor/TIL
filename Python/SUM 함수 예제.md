## SUM 함수 예제
```python
# 리스트를 인자로 갖는 sum 함수
result1 = sum([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])      # 55
print(result1)


# 인자에서 숫자가 아닌 값이 들어가게 되면 error 발생
result2 = sum([1, 2, 'python', 4, 5])               # error
print(result2)


# 튜플을 인자로 갖는 sum 함수
b = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10)                 # 55
result3 = sum(b)
print(result3)


# 비어있는 리스트나 튜플을 인자로 갖는 sum 함수
c = []                                              # 0
d = ()                                              # 0
result4 = sum(c)
result5 = sum(d)

print(result4)
print(result5)
```


참고 블로그: https://blockdmask.tistory.com/413
