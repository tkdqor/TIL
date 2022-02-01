## 함수를 정의할 때, return 값
```python
def is_even(number):
    if number % 2 == 0:
        return True
    else:
        return False

number1 = 10
result = is_even(number1)
print(result)

if result == True:
    print(f'{number1}은 짝수입니다.')
else:
    print(f'{number1}은 홀수입니다.')    
```

- 다음과 같은 코드에서 볼 수 있듯이, 함수를 정의할 때 return 값을 정해줄 수 있다. 이 return은 함수를 실행한 쪽, 함수를 호출한 쪽에 이 값을 활용할 수 있게끔 돌려준다는 의미. 
  - return 값이 있는 함수라면, result = is_even(number1) 이렇게 함수를 호출한 값을 임의의 변수로 정의해줄 수 있다.
