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
