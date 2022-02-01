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
  - return 값이 있는 함수라면, result = is_even(number1) 이렇게 함수를 호출한 값을 임의의 변수로 정의해줄 수 있다. return True가 실행된다면, 우리는 True라는 값을 돌려받게 된다.


```python
result2 = print("HELLO")
print(result2)
```

- 다음과 같은 코드의 경우, 등호를 기준으로 오른쪽에 있는 것이 먼저 실행이 되어야 하니까 터미널 상에 HELLO가 출력이 된다. 
- 그런데, print라는 함수가 별도로 return를 하지 않는 함수이기 때문에 내부적으로 기능만 수행하고 끝나버리는 함수인 것이다. 그래서 돌려줄 return 값이 없으니까, 등호를 기준으로 오른쪽에 print 함수가 돌려준 게 없어서 result2라는 변수를 출력하면 None이라는 아무런 값이 없는 상태를 출력하게 된다.

- 그리고, 함수 내부에서 return이라는 키워드를 가진 코드가 실행될 경우에는 거기서 함수의 역할이 모두 끝나버리고 그 순간 함수가 바로 종료되기 때문에 return True라고 되어있는 그 다음줄에 우리가 코드를 작성한다고 해도, 코드가 실행되지 않는다.

```python
def is_even(number):
    if number % 2 == 0:
        print(f'{number}는 짝수입니다.')
        return True
    else:
        print(f'{number}는 홀수입니다.')
        return False
```

- 따라서, 다음과 같이 return 위에 코드를 추가해주면 된다.


- print함수는 파라미터가 있어서 터미널 화면에 출력하고자 하는 대상을 그 인자로 전달받지만, 별도로 return을 하지 않는 함수라고 볼 수 있다.
- 반면에, input 함수는 파라미터가 있고 return 값도 있다.
  - return 값이 있기 때문에 password = input('password: ') 이런식으로 input함수가 내부적으로 처리한 다음 사용자가 입력한 값이 계산되어서 input 함수 내부에서 return해주기 때문에 그 값을 우리가 돌려받아서 우리가 임의의 변수로 정의하여 활용할 수 있게 되는 것이다.


## 함수 내용 정리
- 함수란, 특정 기능을 사용함에 있어서 일련의 절차가 항상 동일한 방식으로 수행되어야 하는 경우, 사용자로부터 인자(argument)를 전달받아 내부적으로 각종 처리를 한 다음 필요한 경우 그 결과를 함수를 호출한 쪽으로 돌려준다.(return)


### 함수와 for문을 함께 사용한 예시
```python
students = [
    'RYAN',
    'APEACH',
    'BROWN',
    'SALLY'
]

def check_assignment(student):
    print(f'{student}님 안녕하세요!')
    print(f'과제는 다 하셨나요? {student}님?')
    print('고생 많으셨습니다!')


for student in students:
    check_assignment(student)
    
>>>
RYAN님 안녕하세요!
과제는 다 하셨나요? RYAN님?
고생 많으셨습니다!
APEACH님 안녕하세요!
과제는 다 하셨나요? APEACH님?
고생 많으셨습니다!
BROWN님 안녕하세요!
과제는 다 하셨나요? BROWN님?
고생 많으셨습니다!
SALLY님 안녕하세요!
과제는 다 하셨나요? SALLY님?
고생 많으셨습니다!    
```


