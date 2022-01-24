## 연산자
- 더하기나 빼기처럼 프로그래밍에서 연산을 하는데 사용되는 기호를 연산자, 영어로는 Operator라고 부른다. 그 중에서도 2개의 값을 비교하는 연산자를 비교 연산자라고 한다. 
- 여러 개의 Boolean 조건들을 비교할 때는, 논리 연산자를 사용하면 된다.
  - and 연산자는 and를 기준으로 좌우 양쪽이 모두 True일 경우에만 결과가 True로 계산된다. or 연산자의 경우는 좌우에 있는 것들 중 하나라도 True이면 결과가 True가 된다. not 연산자는 True를 False로, False를 True로 기존에 있는 값을 반대로 바꿔주는 역할을 한다.

```python
print(3==3)         # True

print(3 != 3)       # False

print(3 < 3)        # False

print(3 > 3)        # False

print(3 <= 3)       # True

print(3 >= 3)       # True

a = True
b = False

print(a and b)      # False

print(a or b)       # True

print(not a)        # False


name = "sangbaek"
age = 30

print(name == "sangbaek" and age < 30)          # False
```

