## IF문 (If statement)
- 조건문을 사용하기 위한 문법으로 가장 많이 IF문이 사용된다. 

```python
password = "SANGBAEK"
my_input = input("PASSWORD: ")

if password == my_input:
    print("LOGIN")
else:
    print("FAIL")    
```

- if문은 보통 if-else의 형태로 많이 사용된다. 
- if 바로 뒤에는 True나 False를 계산할 수 있는 조건 / 영어로는 condition이 와야한다. 그리고 문장에 끝에는 콜론(:)를 적어줘야 한다. 그래야 python이 여기까지가 if문이라는 것을 인식하기 때문이다. 
- else는 '그렇지 않으면 ~해라' 라는 의미로 condition를 적을 필요가 없고 else:의 형태로 적어주면 된다.

- python에서는 반드시 if문에서 들여쓰기를 해줘야 한다. C언어나 Java 등 다른 프로그래밍 언어에서는 보통 중괄호를 사용해서 코드의 영역을 구분하는데, 파이썬에서는 들여쓰기로 구분하게 된다. 
  - 들여쓰기는 space나 Tab 상관없으나 깊이를 동일하게 맞춰줘야 한다. 만약 if문 시작부터 4칸으로 했으면 계속해서 4칸을 유지해야 한다. 들여쓰기 레벨이 같아야 if의 condition를 만족할 때 실행이 된다.
  - 만약, 들여쓰기가 제대로 되어있지 않는 경우에는 **IndentationError: unexpected indent** 라는 오류가 발생하게 된다.
  - 이러한 들여쓰기가 중요한 또 다른 이유는, if-else문이 끝난 다음의 코드와 if-else문의 코드를 구분하기 위해서이다.

```python
password = "SANGBAEK"
my_input = input("PASSWORD: ")

if password == my_input:
    print("LOGIN")
    print("LOGIN")
    print("LOGIN")
else:
    print("FAIL")    

    print("로그인에 실패하였습니다.")
```

- 다음과 같이 else문에서 **print("로그인에 실패하였습니다.")** 는 -> else에 해당한다. else 다음에 첫번째로 나오는 print("FAIL") 에 사용된 들여쓰기가 4칸인데, 비록 print("로그인에 실패하였습니다.")와 중간에 공백이 있지만, 들여쓰기가 4칸으로 같기 때문에 else 구문에 포함되는 것이다.

```python
password = "SANGBAEK"
my_input = input("PASSWORD: ")

if password == my_input:
    print("LOGIN")
    print("LOGIN")
    print("LOGIN")
else:
    print("FAIL")    

    print("로그인에 실패하였습니다.")


print("로그인 절차가 끝났습니다.")
```

- 만약, else 구문에 포함시키고자 하는 게 아니고 if랑 else가 모두 끝난 다음에 별도의 다른 코드를 수행하고 싶다면 -> 들여쓰기를 지우고 **print("로그인 절차가 끝났습니다.")** 와 같이 새로 작성해주면 된다.
  - 따라서 해당 코드는 if문의 condition이 True이든 False라서 else구문이 수행되든, 그것과는 상관없이 무조건 돌아가는 코드가 된다.


### IF문 중첩해서 사용하기
- python에서는 if문을 중첩해서 사용할 수 있는데 

```python
looks_young = True

if looks_young:
    age = int(input("동안이시네요ㅎㅎ 몇살이세요?(ex. 19): "))

    if age >= 20:
        print("소주 한 병에 1,800원입니다.")
    else:
        print("미성년자에게 술을 팔지 않습니다.")

    print("감사합니다. 또 오세요!") 

else:
    print("소주 한 병에 1,800원입니다.")       
```

- if문에서 주어진 condition이 True라면, 안쪽 코드를 순서대로 수행하게 되는데 오른쪽에 있는 식이 계산된 값이 왼쪽에 age라는 변수에 저장된다. 또한, 사칙연산를 하거나 크기 비교를 하기 위해서 해당 값을 int 함수로 정수로 바꿔준다.
- 그 다음으로 if age >= 20: 는 중첩되어있는 안쪽의 if문으로 위의 if문을 생각하지 않고 독립적인 if문이라고 생각하면 된다. 
- 그리고 print("감사합니다. 또 오세요!") 라는 코드는 -> 들여쓰기가 위랑 다르기 때문에 중첩된 if문에 해당하지 않고 맨 위의 if문 condition이 충족되면 무조건 실행되는 코드이다.


### elif
- if문에 필요한 조건들을 여러 개 나열하기 위해서 if-else 사이에 elif라는 문법을 활용할 수 있다.
- 일반적인 프로그래밍에서는 else if라고 사용되지만, python에서는 elif라고 사용된다. 
- 2개 이상의 condition, 조건을 if문에서 사용할 때는 and, or 등을 활용해서 사용할 수 있다.

```python
age = int(input('나이가 어떻게 되세요?: '))

if age < 30:
    print('30대 미만입니다.')
elif 30 <= age and age <= 32:
    print('30대 초반입니다.') 
elif 32 < age and age <= 36:
    print('30대 중반입니다.')
elif 36 < age and age <= 39:
    print('30대 후반입니다.')
else:
    print('40대 이상입니다.')             
```

- 해당 코드에서는 age가 30 이상이면서 32이하인 경우를 and로 표현했는데,

```python
age = int(input('나이가 어떻게 되세요?: '))

if age < 30:
    print('30대 미만입니다.')
elif 30 <= age <= 32:
    print('30대 초반입니다.') 
elif 32 < age <= 36:
    print('30대 중반입니다.')
elif 36 < age <= 39:
    print('30대 후반입니다.')
else:
    print('40대 이상입니다.')             
```

- Python에서는 다음과 같이 부등호를 사용할 때 and를 기준으로 2번 나눠서 설명하는 것이 아니라 이를 줄여서 **30 <= age <= 32** 이런식으로 양쪽에 부등호를 써서 한 번에 표시할 수도 있다.
- 맨 위에 있는 조건인 age < 30를 만족하지 않는다면 그 다음 elif의 조건이 맞는지 검토하게 된다. 이처럼 elif를 사용하면, if문의 조건 검사 -> elif의 조건 검사 -> 그 어떤 조건도 만족하지 못했다면 else가 실행된다.
  - 만약, 중간의 elif 조건을 만족하게 된다면 들여쓰기를 한 해당 코드를 실행하게 되고 그 뒤의 코드들은 무시하게 된다.
    - ex) 30이라는 숫자를 터미널에 입력했다면, if문 조건인 age < 30이 충족되지 않아 그 다음 elif 조건인 30 <= age <= 32로 오게 되고, age가 30이니까 30 <= age를 충족 / age <= 32라는 조건도 충족하니까 and 기준으로 좌우 condition 모두 True이다. 이렇게 두번째 condition를 만족하기 때문에 print('30대 초반입니다.') 라는 코드가 실행되고 뒤의 남은 코드들은 건너뛰게 된다.
   
```python
age = int(input('나이가 어떻게 되세요?: '))

if age <= 30:
    print('30대 미만입니다.')
elif 30 <= age <= 32:
    print('30대 초반입니다.') 
elif 32 < age <= 36:
    print('30대 중반입니다.')
elif 36 < age <= 39:
    print('30대 후반입니다.')
else:
    print('40대 이상입니다.')   
```

- 다음과 같이 if문 조건과 그 다음 elif 조건처럼 30이라는 숫자가 중첩되게끔 조건을 작성하더라도 -> 조건을 맨 위에서부터 검사하고 조건이 True일 경우에 그 안쪽의 코드를 수행한 다음, 다른 elif나 else를 무시하기 때문에 print('30대 미만입니다.')가 실행된다. 
