# Python


📖 **Contents**

- Python
  - [append와 extend 차이](#append와-extend-차이)
  - [리스트에서 [-1]이란](#리스트에서--1이란)
  - [Python은 인터프리터 언어](#python은-인터프리터-언어)
  - [Flask와 Django](#flask와-django)
  - [리스트와 튜플 그리고 딕셔너리의 차이](#리스트와-튜플-그리고-딕셔너리의-차이)
  - [리스트에서 중복 제거](#리스트에서-중복-제거)
  - [call by assignment란](#call-by-assignment란)
  - [function에서 def와 lambda의 차이](#function에서-def와-lambda의-차이)
  - [클래스에서 self를 사용하는 이유](#클래스에서-self를-사용하는-이유)
  - [pip란](#pip란)
  - [python 관련 블로그](#python-관련-블로그)
  - [python 관련 블로그2](#python-관련-블로그2)

* * *

## append와 extend 차이
```python
a = [1,2,3]
b = [4,5]

b.append(a)
print(b)

[4, 5, [1, 2, 3]]

# 이번엔 문자열
a = "son"
b = [4,5]

b.append(a)
print(b)

[4, 5, 'son']
```

- append 함수는 형태 그대로 요소가 리스트 맨 뒤에 추가된다.

```python
a = [1,2,3]
b = [4,5]

b.extend(a)
print(b)

[4, 5, 1, 2, 3]

# 이번엔 문자열
a = "son"
b = [4,5]

b.extend(a)
print(b)

[4, 5, 's', 'o', 'n']
```

- extend 함수는 요소를 리스트 맨 뒤에 추가하지만, iterable의 모든 항목들을 넣어준다.

* * *

## 리스트에서 -1이란
```python
a = [1,2,3]
print(a[-1])

3
```

- 리스트에서 [-1]이란 리스트의 맨 마지막 값을 가져오는 것이다.

* * *

## Python은 인터프리터 언어
- **Python은 컴파일러(compiler)를 사용하는 C언어나 자바와는 달리 인터프리터에 의해 실행되는 스크립트 언어이다.**
- 인터프리터(interpreter)란 소스 코드를 처음부터 한 라인씩 차례대로 해석하며 실행하는 프로그램을 의미한다.
  - 인터프리터는 실행할 때마다 코드를 한 라인씩 바로 기계어로 변환하기 때문에 실행 속도가 컴파일러 언어에 비해 다소 느리지만, 코드가 완전히 작성되지 않아도 작성된 부분까지만을 테스트할 수 있다.

* * *

## Flask와 Django
- python framework 중 Django는 웹 서비스 호스팅용으로 사용하기 적합하며 CRUD를 수행하기 위한 최적의 프레임워크이다. 다만 빠른 웹 프레임워크는 아니어서 무겁다는 단점이 있다.
  - Django는 한 프로젝트 내에 다양한 어플리케이션이 존재 할 수 있다. 그리고 Django는 ORM이 있어 DB에 접근하기에 편리하다. 또한, 이미 많은 것이 개발되어 있어 내가 원하는 기능을 유연하게 추가할 수 없다.
- python framework 중 Flask는 micro framework이기에 Django보다 가볍고, 개발 속도가 매우 빠르다는 장점이 있다. 다만 개발자가 많은 것을 설정하고 세팅해야 한다.
  - Flask는 프로젝트마다 1개의 어플리케이션을 개발하도록 되어있다. 그리고 따로 ORM이 없기 때문에 DB에 접근할 때 SQLAlchemy 등을 사용해야 한다. 또한, 확장과 커스터마이징이 편리하다.
- 최근에는 fast API 가 이 둘의 장점을 결합해 놓은 framework라고 들었다.


* * *

## 리스트와 튜플 그리고 딕셔너리의 차이
- 리스트 : 대괄호 표시를 사용하고 element(요소)의 수정, 삽입, 삭제가 가능
- 튜플 : 소괄호 표시를 사용하고 element의 변경이 불가능, 순서는 변경 가능
- 딕셔너리 : 중괄호 표시를 사용하고 element는 한 쌍의 key:value로 표현

* * *

## 리스트에서 중복 제거
```python
a = [1,2,2,2,3]
b = set(a)

print(b)
print(list(b))

{1, 2, 3}
[1, 2, 3]
```

- set 자료구조의 특징이 중복이 불가능하다는 점을 이용해서 -> 위와 같이 리스트 자료형을 set 타입으로 변경해서 중복을 제거할 수 있다.
- 그 후에 다시 list() 함수를 사용해서 리스트 타입으로 데이터를 변경할 수 있다.

* * *

## call by assignment란
- Call-by-value는 값에 의한 호출을 의미하고, Call-by-reference는 참조(주소)에 의한 호출을 의미한다.
  - **Call-by-reference**는 함수 호출시 인자로 전달되는 변수의 레퍼런스를 전달한다. 인자로 전달된 레퍼런스는 함수 내부에서 실제 값이 반영되며 C언어의 포인터가 대표적인 예시다. 따라서 실제 값이 바뀌면 인자로 전달된 값도 바뀐다.
  - **Call-by-value**는 함수 호출시 전달되는 변수의 값을 복사해서 함수의 인자로 전달된다. 따라서 함수 안에서 인자 값이 변경되도 전달한 외부 변수에는 영향이 가지 않는다.

- **Python에서는 call-by-assignment 방식으로 인자를 전달한다.**
  - Python은 명시적인 Call by Reference 혹은 Call by Value 라는 개념이 존재하지 않는다.
  - Python의 function에 argument(인자)를 전달할 때, Manual에 따르면 call by assignment 라는 방식으로 전달된다.
  - **Python은 2가지로 객체를 구분할 수 있다.**


- **immnutable object**
  - 변경 불가능한 객체라는 뜻으로 일반적인 자료형인 int, float, str과 tuple이 있다.
  - immutable 객체는 값이 변경되면 객체 자체가 변경되며, 여기에 속한 객체는 **call-by-value** 이다.
  - **따라서 변수를 복사 할 때 새로운 객체가 생성되며 변경이 일어날 때 기본 객체는 변하지 않는다.**

```python
def foo(a):
    a += 2
    print("local : " + str(a))
    return a

a = 10
foo(a)
print(a)

# call-by-value로 기본 객체인 a는 10으로 변하지 않는다.
local : 12
10
```


- **mutable object**

  - 변경 가능한 객체라는 뜻으로 list, dictionary, set이 있다.
  - mutable 객체는 값이 변경되면 주소가 참조하는 값이 모두 변경되며, 여기에 속한 객체는 **call-by-reference** 이다.
  - **따라서 변수를 복사할 때 주소가 참조하는 값이 모두 변경된다.**

```python
def foo2(a):
    a.append(1);
    print("local : " + str(a))

a = []
foo2(a)
print(a)

# call-by-reference로 기본 객체인 a가 변경되었다.
local : [1]
[1]
```

* * *

## function에서 def와 lambda의 차이
- 일정 주기 이상 유효해야 하고, 여러 번 재사용해야 한다면 def로 만들어 처리하는 것이 유용
```python
def add(x,y):
    return x + y

print(add(1,2))
3
```

- 간단한 일회성 함수를 만들어 사용하려면 한 줄로 간결하게 구현할 수 있는 lambda 함수가 유용
```python
add = lambda x,y: x + y
print(add(1,2))
3

# 또는 아래처럼 이름없이 사용 가능
print((lambda x,y: x + y)(10, 20))
30
```

* * *

## 클래스에서 self를 사용하는 이유
- self는 객체의 인스턴스 그 자체를 말한다. 즉, 객체 자기 자신을 참조하는 매개변수 역할을 한다. 객체지향 언어는 모두 이걸 메소드에 안보이게 전달하지만, 파이썬은 클래스의 메소드를 정의할 때 self를 명시한다. 그래서 메소드를 불러올 때 self는 자동으로 전달된다. self를 사용함으로 클래스내에 정의한 멤버에 접근할 수 있게된다.


* * *

## pip란
- pip는 파이썬으로 작성된 패키지 라이브러리들을 관리해주는 시스템이다. 파이썬 개발 환경에 패키지들을 설치하기 위해서 pip 명령을 자주 사용하게 된다.


* * *

## python 관련 블로그
- https://han.gl/JaNWX


* * *

## python 관련 블로그2
- https://uiandwe.tistory.com/1272?category=923644




