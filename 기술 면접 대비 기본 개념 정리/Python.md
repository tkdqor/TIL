# Python


📖 **Contents**

- Python
  - [문법 관련 내용](#문법-관련-내용)
  - [Python으로 개발공부를 시작한 이유](#python으로-개발공부를-시작한-이유)
  - [Python의 장점과 단점](#python의-장점과-단점)
  - [assert란](#assert란)
  - [append와 extend 차이](#append와-extend-차이)
  - [리스트에서 [-1]이란](#리스트에서--1이란)
  - [컴파일과 인터프리터](#컴파일과-인터프리터)
  - [Python의 동작원리](#python의-동작원리)
  - [Flask와 Django 그리고 FastAPI](#flask와-django-그리고-fastapi)
  - [리스트와 튜플 그리고 딕셔너리의 차이](#리스트와-튜플-그리고-딕셔너리의-차이)
  - [리스트에서 중복 제거](#리스트에서-중복-제거)
  - [call by assignment란](#call-by-assignment란)
  - [function에서 def와 lambda의 차이](#function에서-def와-lambda의-차이)
  - [클래스에서 self를 사용하는 이유](#클래스에서-self를-사용하는-이유)
  - [pip란](#pip란)
  - [resolve 함수](#resolve-함수)
  - [\_\_name\__ 변수](#던더name던더-변수)
  - [datetime 모듈](#datetime-모듈)
  - [gettext_lazy](#gettext_lazy)
  - [python으로 파일 읽고 쓰기](#python으로-파일-읽고-쓰기) 
  - [매직 메소드](#매직-메소드)
  - [\_\_class\__ 메소드](#던더class던더-메소드)
  - [\_\_new\__ 메소드와 \_\_init\__ 메소드의 차이](#던더new던더-메소드와-던더init던더-메소드의-차이)
  - [변수가 있는지 확인하는 hasattr](#변수가-있는지-확인하는-hasattr)
  - [python에서 상수 처리 하기](#python에서-상수-처리-하기)
  - [GIL이란](#gil이란)
  - [python 멀티 쓰레드와 멀티 프로세스](#python-멀티-쓰레드와-멀티-프로세스)
  - [MRO란](#mro란)
  - [변수의 메모리 주소를 확인하는 id 함수](#변수의-메모리-주소를-확인하는-id-함수)
  - [flag 변수](#flag-변수)
  - [python PEP 8이란](#python-pep-8이란)
  - [args와 kwargs란](#args와-kwargs란)
  - [Pandas란](#pandas란)
  - [Numpy란](#numpy란)
  - [가비지 컬렉터란](#가비지-컬렉터란)
  - [딕셔너리와 리스트에서 순차적으로 요소를 조회할 때 시간복잡도 비교](#딕셔너리와-리스트에서-순차적으로-요소를-조회할-때-시간복잡도-비교)
  - [property 사용하기](#property-사용하기)
  - [super 함수](#super-함수)
  - [classmethod와 staticmethod](#classmethod와-staticmethod)
  - [python에서 비동기 코드 작성하기](#python에서-비동기-코드-작성하기)

* * *

## 문법 관련 내용
- [문법 관련 레포지토리](https://github.com/tkdqor/coding_test_practice/blob/master/Collection%20of%20ideas.md)

* * *

## Python으로 개발공부를 시작한 이유
- 처음 개발자가 되고 싶다는 생각을 한 이후에 python이라는 언어가 가장 접하기 쉬운 언어라고 생각해서 시작하게 되었다. 그리고 java와는 다르게 필수적으로 type을 선언하지 않아도 된다고 알고 있어 더 편리하고 "파이써닉하다"라는 말이 있듯이 코드 자체가 깔끔하다는 생각이 들어 python 개발자가 되고 싶다는 생각을 하게 되었다.
- 또한, django와 같이 웹 프레임워크를 접하는데 있어 기반이 되는 언어라는 점이 처음 웹 개발을 시작하는 입장에서 선택할 수 있는 언어라고 판단했다.

- [관련 블로그](https://with-alpha-and-omega.medium.com/java-java-%EC%99%80-python-%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90-eecc1e0e8f00)

* * *

## Python의 장점과 단점
- **장점** : python은 문법이 다른 언어에 비해서 간결하게 구성되어 있다. 그래서 이러한 이유로 오류 발생을 줄일 수 있고, 조금 더 빠르게 개발을 진행해나갈 수 있다는 장점이 있다. 또한 python을 기반으로 django나 fastAPI, Flask와 같은 다양한 웹 프레임워크를 사용할 수 있다는 점도 큰 장점이다. 그리고 python은 다양한 라이브러리가 존재하여 많은 분야에 사용될 수 있다.
  - 또한, 동적 타입으로 인해 유연하다. 즉, 자료형 타입에 대해 매우 자유롭다.

- **단점** : python은 컴파일 언어가 아닌 인터프리터에 의해 실행되는 스크립트 언어이기 때문에 실행할 때마다 코드를 한 라인씩 바로 기계어로 변환하는 과정을 거쳐야 한다. 그래서 실행속도가 다소 느린 점이 단점이라고 볼 수 있다.
  - 그리고 런타임 시에 자료형 오류가 발생할 수 있다. 런타임에서 값은 예상치 못한 유형이 있을 수 있고, 그 자료형에 대한 무의미한 작업이 적용된다.
  - 이런 오류들을 런타임으로 실행하고 나서야 확인할 수 있다.

<br>

- **정적 타입**
  - 자바와 같은 언어는 타입(자료형)을 지정해줘야 한다. 컴파일 시에 자료형이 맞지않으면 컴파일 에러가 발생한다. 타입 에러를 런타임이 아니라 컴파일 시에 발견할 수 있어서 보다 안전하다. 컴파일 시에 타입에 대한 정보를 결정하기 때문에 속도가 빠르다. C, C++, java, ..

- **동적 타입**
  - Python은 동적타입을 사용한다. 타입을 지정하지 않고 자유롭게 사용하여 편리하게 개발할 수 있다. 따라서 빠르게 개발할 수 있다.
Python, Ruby, SmallTalk, JavaScript, ..
  - 단점은 느리고 런타임 시에 TypeError 가 발생할 수 있다.


* * *

## assert란
- **python에서 assert란, 어떤 조건이 참이라는 것을 보장하고, 만약 거짓이라면 AssertionError와 함께 오류 메시지를 띄울 수 있는 것을 의미한다.**

- **assert [조건], [오류 메시지]**
  - assert 키워드 뒤에 [조건]을 입력하고 그 뒤에 콤마(,) [오류메시지]를 입력한다. 이 조건이 True이면 그대로 코드가 진행되고, False라면 AssertionError가 발생한다. 앞에 조건이 False인 경우, AssertionError와 함께 남길 메시지가 바로 오류 메시지를 의미한다.

```python
a = 10

print("2. assert False 인 경우")
assert a == 999, "a is not 999"

print("끝2")

>>> AssertionError : a is not 999
```

- **이렇게 assert 다음의 조건이 False인 경우, AssertionError와 함께 입력한 오류 메시지가 출력된다.**

- **DRF class BaseSerializer 내부 메서드 코드 예시**
```python
class BaseSerializer(Field):
    ...
    validated_data = {**self.validated_data, **kwargs}

    if self.instance is not None:
         self.instance = self.update(self.instance, validated_data)
         assert self.instance is not None, (
             '`update()` did not return an object instance.'
         )
    else:
         self.instance = self.create(validated_data)
         assert self.instance is not None, (
             '`create()` did not return an object instance.'
         )

    return self.instance
```


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

## 컴파일과 인터프리터
- 컴퓨터의 언어는 0과 1로 이루어져있는 기계어이다. 반면에, 우리가 다루는 언어는 프로그래밍 언어이다. 그래서 컴퓨터와 우리는 서로 다른 언어를 사용하고 있다.
  - **따라서, 최종적으로 if, else와 같은 프로그래밍 언어를 0과 1의 기계어로 번역해서 컴퓨터에게 전달해줘야 한다.** 
  - **이러한 방법에는 컴파일과 인터프리터 2가지 방법이 존재한다.**

- ex) 내가 현재 터키에 살고 있고 다들 터키어로 이야기하고 있다. 근데 나는 영어를 하고 있어서 이해를 할 수 없다. 내가 거리를 걷는데 터키 사람이 터키어로 이야기하면 뭔말인지 모른다. 이러한 상황에서
  - **인터프리터**는 내 옆에 서서 귀에다 대고 바로바로 영어로 통역를 해주는 통역사라고 볼 수 있다. 터키어가 나에게로 오고 실시간으로 영어로 통역해주는 것이다. 즉, 실시간으로 통역해준다는 점이 중요하다.
  - 반면에 **컴파일러**는 일단 실시간으로 진행되는 것이 아니다. 통역해주는 내 친구가 일단 터키 친구의 이야기를 듣고, 터키 친구가 뭐라 하는지 이해를 한 후에, 전체 메시지를 나에게 통역해주는 것이다.
  - **즉, Interpretation은 실시간이고 / Compilation은 실시간이 아니라는 차이가 있다. 그래서 이러한 차이점으로 인해 어떤 언어는 더 느리고, 더 빠르게 만드는 것이다.**

- **ex) Python은 인터프리터 언어이고 C언어는 컴파일 언어이다.**
  - 그래서 Python은 컴퓨터한테 실시간으로 0과 1을 통역해준다. 코드 한 줄씩 말이다. 이건 느린 프로세스일 수 밖에 없는 게, 중간에 Python 언어를 0과 1로 통역하는 중간 프로세스가 필요하다. 컴퓨터 입장에서는 조금씩 0과 1 언어로 번역해주는 것이기 때문에 느리게 작업할 수 밖에 없다.
  - 반면에 C언어는 Compiled 되어있다. 코딩을 다 하고나서, ‘컴파일러’가 C언어로 작성된 모든 코드를 0과 1로 번역해서 파일을 만든 다음, 그 후에 프로그램을 실행시킨다. 그래서 더 빠를 수 밖에 없다. 이미 0과 1로 다 작업해서 만든 걸로 실행시키는 것이기 때문이다. 컴퓨터 입장에서는 프로그램을 열면 이미 0과 1로 되어있기 때문에 빠르게 작업할 수 있다.

- **컴파일 언어와 인터프리터 언어의 차이점**
  - Python, 자바스크립트와 같은 인터프리터 언어는 실시간이다 보니까 에러가 생길 수 있다. 그리고 에러가 나타날 때까지 전혀 그 상황을 모를수도 있다. 우리는 에러가 생기기전에 알고 싶기 때문에 이러한 점이 단점이된다.
  - 반대로 C언어와 같이 complied 언어의 경우에는, 컴파일을 하려고 할 때 에러가 있으면 고치라고 알려준다. 그래서 이렇게 수정하고나서 컴파일을 하게되면, 거기에 더이상 에러가 없다는 것을 알 수 있다. 이미 컴파일러가 확인을 했기 때문이다.


* * *

## Python의 동작원리
- **Python은 컴파일러(compiler)를 사용하는 C언어나 자바와는 달리 인터프리터에 의해 실행되는 스크립트 언어이다.**
- 인터프리터(interpreter)란 소스 코드를 처음부터 한 라인씩 차례대로 해석하며 실행하는 프로그램을 의미한다.
  - 인터프리터는 실행할 때마다 코드를 한 라인씩 바로 기계어로 변환하기 때문에 실행 속도가 컴파일러 언어에 비해 다소 느리지만, 코드가 완전히 작성되지 않아도 작성된 부분까지만을 테스트할 수 있다.

### Python은 컴파일 과정이 있는 인터프리터 언어
- 파이썬은 대표적인 인터프리터 방식 언어이다. 하지만 실제로는 인터프리터 안에서는 컴파일이 이루어진다. 
- **파이썬으로 코드를 작성하면 내부적으로 CPython이라는 컴파일러가 코드를 바이트코드로 컴파일하고, 이것을 CPython 인터프리터가 실행하는 순서로 작동한다.**
  - Python을 C언어로 구현한 구현체인 CPython이 Python의 인터프리터이면서 컴파일러이다. 우리가 작성하는 Python 코드를 bytecode로 컴파일하고 실행한다. 그 다음에 interpreter(virtual machine)를 실행해서 0과 1로 이루어져있는 기계어로 번역하는 과정을 진행해서 컴퓨터에게 전달하는 것이다.

- .py 파일을 실행하면 .pyc 라는 파일이 생성되는데 이것이 CPython이 컴파일한 bytecode가 들어있는 것이다. 그 다음 .pyc를 interpreter 하는 것도 CPyton이다.
  - Ex) 진행했던 프로젝트 디렉터리 구조를 보면, 생성한 앱 디렉터리별로 \_\_pycache\__ 라는 이름의 디렉터리가 있다. 그리고 그 디렉터리 내부에는 urls.cpython-39.pyc와 같은 이름의 파일들이 여러 개가 있는데, 이 파일들이 바로 CPython이 컴파일한 bytecode가 들어있는 파일들이 생성된 것이다.

- CPython 인터프리터 실행 중에 단점이 있는데 GIL(global interpreter lock)을 사용한다는 것이다. bytecode를 실행할 때에 여러 thread를 사용할 경우, 전체에 lock 을 걸어서 한번에 단 하나의 thread 만이 python object에 접근하도록 제한한 것이다. 하지만 single thread일 때는 문제가 없고 GIL의 단점을 보안하기 위한 방법들이 존재하고 있어서 GIL로 인한 불편함을 느낄 가능성은 거의 없다고 한다.

- [관련 블로그](https://yomangstartup.tistory.com/108), [관련 블로그2](https://cjh5414.github.io/about-python-and-how-python-works/)


* * *

## Flask와 Django 그리고 FastAPI
- python framework 중 **Django**는 웹 서비스 호스팅용으로 사용하기 적합하며 CRUD를 수행하기 위한 최적의 프레임워크이다. 다만 빠른 웹 프레임워크는 아니어서 무겁다는 단점이 있다.
  - Django는 한 프로젝트 내에 다양한 어플리케이션이 존재 할 수 있다. 그리고 Django는 ORM이 있어 DB에 접근하기에 편리하다. 또한, 이미 많은 것이 개발되어 있어 내가 원하는 기능을 유연하게 추가할 수 없다.
- python framework 중 **Flask**는 micro framework이기에 Django보다 가볍고, 개발 속도가 매우 빠르다는 장점이 있다. 다만 개발자가 많은 것을 설정하고 세팅해야 한다.
  - Flask는 프로젝트마다 1개의 어플리케이션을 개발하도록 되어있다. 그리고 DB에 접근할 때는 ORM의 한 종류인 SQLAlchemy을 사용하게 된다. 또한, 확장과 커스터마이징이 편리하다.
 
- 최근에는 **fast API**라는 framework도 존재한다. 
  - django와 다르게 비동기 통신을 지원하고 빠르다는 장점이 있다. 또한, API 문서를 자동으로 만들어준다. 그리고 django의 경우 프로젝트 및 앱을 생성하면 알아서 디렉터리가 생성되고 디자인 패턴이 적용되지만, FastAPI의 경우에는 main.py에서 시작해서 아무런 구성이 되어있지 않아서 개발자가 많은 것을 커스텀해야 한다.

* * *

## 리스트와 튜플 그리고 딕셔너리의 차이
- 리스트 : 대괄호 표시를 사용하고 element(요소)의 수정, 삽입, 삭제가 가능
- 튜플 : 소괄호 표시를 사용하고 element의 변경이 불가능. 리스트보다 메모리를 더 적게쓰고 빠르다.
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
    - [Call by reference 내용](https://github.com/tkdqor/TIL/blob/main/%EA%B8%B0%EC%88%A0%20%EB%A9%B4%EC%A0%91%20%EB%8C%80%EB%B9%84%20%EA%B8%B0%EB%B3%B8%20%EA%B0%9C%EB%85%90%20%EC%A0%95%EB%A6%AC/ETC.md#call-by-reference%EB%9E%80)
  - **Call-by-value**는 함수 호출시 전달되는 변수의 값을 복사해서 함수의 인자로 전달된다. 따라서 함수 안에서 인자 값이 변경되도 전달한 외부 변수에는 영향이 가지 않는다.

- **Python에서는 call-by-assignment 방식으로 인자를 전달한다.**
  - Python은 명시적인 Call by Reference 혹은 Call by Value 라는 개념이 존재하지 않는다.
  - Python의 function에 argument(인자)를 전달할 때, Manual에 따르면 call by assignment 라는 방식으로 전달된다.
  - **Python은 2가지로 객체를 구분할 수 있다.**


- **immnutable object(불변객체)**
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


- **mutable object(가변객체)**

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
- self는 객체의 인스턴스 그 자체를 말한다. 즉, 객체 자기 자신을 참조하는 매개변수 역할을 한다. 객체지향 언어는 모두 이걸 메소드에 안보이게 전달하지만, 파이썬은 클래스의 메소드를 정의할 때 self를 명시한다. 그래서 메소드를 불러올 때 self는 자동으로 전달된다. self를 사용함으로 클래스내에 정의한 메소드에 접근할 수 있게된다.
  - **그래서 클래스 내부 메소드안에 변수를 정할 때 -> self.변수명 / 클래스 내부의 메소드를 정할 때 def 메소드명(self):**
  - 클래스 내부에 정하는 클래스변수는 self를 붙이지 않고 정의한다.

* * *

## pip란
- pip는 파이썬으로 작성된 패키지 라이브러리들을 관리해주는 시스템이다. 파이썬 개발 환경에 패키지들을 설치하기 위해서 pip 명령을 자주 사용하게 된다.

* * *

## resolve 함수
```python
# urls.py
urlpatterns = [
    path('jobpostings/', JobPostingsAPIView.as_view(), name='sangbaek'),
    ...
]
```

```python
# views.py
from django.urls import resolve

match = resolve('/jobpostings/')
print(match.url_name)

>>> sangbaek
```

- **위와 같이 resolve 함수에 urls.py에 정의한 url path를 입력하고 url_name 메소드를 사용하면, urls.py에 정의한 url name를 반환해준다.**

- [django 공식문서](https://docs.djangoproject.com/ko/4.0/ref/urlresolvers/)
- [관련 블로그](https://ugaemi.github.io/django/Django-reverse-and-resolve/)

* * *

## 던더name던더 변수
- **\_\_name\__ 변수는 해당 python 파일의 이름, 즉 모듈의 이름을 담게되는 변수이다.**
  - ex) function1.py의 \_\_name\__ 변수는 function1이 된다. \_\_name\__ == function1

- **그런데, 해당 python 파일 안에서의 \_\_name\__ 변수는 ==> 모듈이름이 아닌, \_\_main\__ 이라는 이름의 값이 된다.**
- 우리가 종종 볼 수 있는 

```python
if __name__ == "__main__":
    함수이름()
```

- **이러한 코드의 의미는, 해당 함수를 꼭 해당 함수가 정의되어있는 파일에서만 실행할 수 있게끔 설정한 것이다. 즉, 다른 파일에서 import 했을 때, 그 모듈안에 있는 모든 코드들이 그대로 실행되는 것을 막아줄 수 있다.**
- **다른 파일에서 function1.py를 import 해서 \_\_name\__ 값을 확인해보면, function1이 된다.**

- [관련 블로그](https://lovelydiary.tistory.com/297)

* * *

## datetime 모듈

- **datetime.timedelta**
  - python 내장 모듈인 datetime 모듈의 timedelta 클래스를 이용해서 기간을 표현할 수 있다. timedelta 클래스의 생성자는 주, 일, 시, 분, 초, 밀리 초, 마이크로 초를 인자로 받는다.

```python
from datetime import timedelta

timedelta(days=5, hours=17, minutes=30)

# 또는 이렇게도 표현 가능
import datetime

time_limit = 180
datetime.timedelta(seconds=time_limit))
```

```python
import datetime

data = datetime.timedelta(seconds=180)
print(data)

>>> 0:03:00
```

- 위와 같이 코드에서 사용해볼 수 있다.

- [관련 블로그](https://www.daleseo.com/python-datetime/)

* * *

## gettext_lazy
-  \_("Token is invalid or expired")와 같이 문자열에 묶어서 사용하기 위해 설정해주는 코드이다. gettext_lazy는 장고의 번역기능을 지원해줄 수 있는 함수이다.
```python
from django.utils.text import gettext_lazy as _

default_error_messages = {"bad_token": _("Token is invalid or expired")}
```

- 위와 같이 보통 as로 언더바를 설정해서 gettext_laxy를 사용하게 된다.
- 이 함수를 사용해서 문자열을 정의하면, 이후에 django의 다국어 처리 기능과 연동할 수 있게 된다. 사용자가 한국, 미국 등 접속한 위치에 따라서 다른 언어가 return 되게 할 수 있다. 그래서 다국어를 지원하는 서비스를 만들 때 유용하게 사용할 수 있다.

* * *

## python으로 파일 읽고 쓰기
- **파일 생성 open, close 함수**
  - 파일 객체를 열기 위해서는 open() 함수를 사용하고 이렇게 열었으면 반드시 close()라고 하는 함수를 사용해서 파일 객체를 닫아줘야 한다.
  - open 함수는 첫번째 인자로 파일 경로를 넣어주고, 두번째 인자로 파일의 옵션 모드를 선택해준다.

```python
# 파일 열기
f = open('C:/Test/t1.txt', 'w') 

# 파일에 텍스트 쓰기
f.write('blockdmask blog')
f.write('\npython open func') 

# 파일 닫기
f.close()

>>> blockdmask blog
>>> python open func
```

- **파일 쓰기 write, writelines 함수**
  - 파일을 open() 함수로 연 이후에 위와 같은 함수들로 텍스트를 작성할 수 있다.
  - write 함수는 매개변수로 파일에 넣을 문자열을 받는다.
  - writelines 함수는 매개변수로 파일에 넣을 문자열 리스트를 받는다.

```python
# 파일 a 모드로 열기 (이미 있는 파일에 이어서 쓰기)
f = open('C:/Test/t2.txt', 'a') 

# writelines 함수를 이용해서 파일에 문자열들 쓰기
f.writelines(['a', 'b', '123', '456', 'abcdefg', '\n']) 

# writelines 함수에 join 을 이용해서 문자열들에 자동 개행 넣기
f.writelines('\n'.join(['BlockDMask', 'python', 'blog'])) 

# 파일 닫기f.close()

>>> ab123456abcdefg
>>> BlockDMask 
>>> python
>>> blog
```

- **파일 읽기 관련, read, readline, readlines, seek, tell 함수**
  - read(n) 함수는 해당 파일의 문자 n개를 가져온다.
  - readline() 함수는 파일로부터 해당 위치에서 한 줄의 문자열을 가져온다. 즉, '\n' 개행이 있을때까지 문자열을 읽어온다.
  - readlines() 함수는 해당 위치에서부터 파일의 모든 문자열을 읽어온다. 개행을 포함해서 가져온다.
  - seek(위치) 함수는 해당 위치로 파일의 커서를 옮기는 함수이다. 파일의 맨 처음 위치는 0이다.
  - tell() 함수는 현재 커서의 위치를 반환하는 함수이다. 

- **관련 예제는 아래 블로그 참고하기**

- [관련 블로그](https://blockdmask.tistory.com/454)

* * *

## 매직 메소드
- **클래스 안에 정의된 함수를 우리는 특별히 '메소드(method)'라고 부르는데, 그 메소드 중에서도 던더로 시작해서 던더로 끝나는 메소드들을 매직 메소드라고 부른다.**
  - 가장 유명한 매직 메소드에는 \_\_init\__이라는 생성자가 있다.
- **이러한 매직 메소드는 클래스를 사용할 때 정의되어있다.**  
  - ex) python에서는 문자열 사이를 + 연산으로 입력해서 실행하면 문자열이 연결되는데, 문자열이 연결되서 실행되는 이유는 python 문자열 타입이 덧셈 연산에 대해서 문자열이 더해지도록 정의해놓았기 때문이다. 이렇게 클래스의 동작을 정의해놓을 수 있게 해주는 메소드가 바로 매직 메소드이다.
  - ex) 파이썬에서 함수의 호출은 함수의 이름에 '( )'를 붙여주면 된다. 이렇게 할 수 있는 이유는, 어떤 클래스(타입)의 객체가 있을 때 '( )'를 붙여주면 해당 클래스에 정의된 매직 메소드인 \_\_call\__이 호출되기 때문이다.

- [관련 내용](https://wikidocs.net/83755)

* * * 

## 던더class던더 메소드
- 특정 객체의 정의 되어있는 상위 클래스를 의미.

```python
...
if obj.__class__ == get_user_model():
...

```

* * *

## 던더new던더 메소드와 던더init던더 메소드의 차이
- \_\_new\__ 메소드는 클래스 객체가 생성되면 먼저 실행 생성자라서 객체의 메모리 할당을 진행하는 객체 속성이라고 할 수 있다.
- \_\_init\__ 메소드는 클래스 생성자로 초기 셋팅을 진행한다. 생성자는 어떤 클래스의 인스턴스(객체)가 생성될 때 파이썬 인터프리터에 의해 자동으로 호출되는 메소드이다.
- **그래서 우리가 보통 클래스를 정의할 때 \_\_init\__ 만을 주로 사용하게 되는데, \_\_new\__ 는 클래스에 정의되어 있지 않으면 알아서 object 클래스의 \_\_new\__ 가 호출되어 객체가 생성되는 것이다. 그 다음, 이렇게 생성된 객체에 속성(property)을 추가할 때 \_\_init\__ 이 호출된다.**

- [관련 블로그](https://www.delftstack.com/ko/howto/python/python-new-keyword/)

* * *

## 변수가 있는지 확인하는 hasattr
- hasattr는 특정 클래스가 해당 변수 및 메소드가 있는지 확인하는 함수이다.
  - 이외에도 클래스의 변수 값을 가져오는 getattr 함수와 클래스의 변수에 값을 설정할 수 있는 setattr 함수가 있다.

```python
class cls:
    a = 1
    def b(self):
        pass

# cls에 b라는 멤버가 있는지 확인
>>> hasattr(cls, 'b')
True

# cls에서 a변수의 값 가져오기
>>> getattr(cls, 'a')
1

# cls의 a라는 변수에 값 9 설정하기
>>> setattr(cls, 'a', 9)
```

- [관련 내용](https://wikidocs.net/13945)

* * *

## python에서 상수 처리 하기
- **상수(constant)는 변하지 않는 수를 의미한다.**
- 하지만, python에서는 상수를 만들 수 있는 기본 문법이 존재하지 않는다. 대신 상수 처리를 할 수 있는 여러가지 방법이 있는데, 대표적으로 **대문자로 변수를 설정**하는 방법이 있다.
  - 실제로는 값을 변경할 수 있지만, 프로그래머 사이에서는 상수를 대문자로 사용하는 것이 오랜 관습이라고 한다. 따라서, 대문자로 변수를 사용하는 것이 상수라는 의미를 줄 수 있다.

```python
PI = 3.14
GRAVITY = 9.8
```

- [관련 블로그](https://smoothiecoding.kr/python-constant-variable/)

- [python에서 상수 처리하는 여러 방법들](https://velog.io/@pm1100tm/Python-%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%97%90%EC%84%9C-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%83%81%EC%88%98%EB%A5%BC-%EC%A0%95%EC%9D%98%ED%95%98%EA%B3%A0-%EC%82%AC%EC%9A%A9%ED%95%A0%EA%B9%8C)
  - 상수를 모듈단위로 선언하기
  - Type.hint 사용해서 좀 더 명확하게 정의하기
  - 상수를 정의할 클래스를 만들어서 정의하기
  - 클래스와 데코레이터로 상수 정의하기

- Type.hint 예시(python 3.5버전 이상에서 사용 가능)
```python
def greeting(name: str) -> str:
    s: str = 'Hello ' + name
    return s
```

* * *

## GIL이란
- **GIL이란, Global Interpreter Lock의 약자로 파이썬 인터프리터(ex. CPython)가 한 스레드만 하나의 바이트코드를 실행시킬 수 있도록 해주는 Lock을 의미한다. 하나의 스레드에 모든 자원을 허락하고 그 후에는 Lock을 걸어 다른 스레드는 실행할 수 없게 막아버리는 것이다.**
  - 그래서 python에서 여러개의 스레드를 통해 작업하는 경우, 병렬적으로 작업을 하는 것이 아니라 각각의 스레드는 GIL를 얻고 동작하게 되고 이 때 다른 스레드는 모두 동작을 멈추게 된다. 

- **python에서 GIL를 사용하는 이유**
  - python에서 [가비지 컬렉터](https://github.com/tkdqor/TIL/blob/main/%EA%B8%B0%EC%88%A0%20%EB%A9%B4%EC%A0%91%20%EB%8C%80%EB%B9%84%20%EA%B8%B0%EB%B3%B8%20%EA%B0%9C%EB%85%90%20%EC%A0%95%EB%A6%AC/Python.md#%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%ED%84%B0%EB%9E%80) 방식이 Reference Counting이기 때문에 python의 모든 객체는 해당 객체가 참조된 수를 저장하고 있다. 
  - 그래서 멀티스레드인 경우에는 여러 스레드가 하나의 객체를 사용한다면, reference count를 관리하기 위해서 모든 객체에 대한 lock이 필요하다. 즉, 만약 python에서 여러개의 스레드가 하나의 객체에 접근하게끔 허용하면 reference count에 중복이 발생할 수도 있을 것이다.
  - 이러한 비효율을 막기 위해 python에서 GIL를 사용하게 되었다. 하나의 lock를 통해 모든 객체들에 대한 refernce count의 동기화 문제를 해결한 것이다.

- **다만, python에서 무조건 멀티스레드가 싱글스레드보다 느린 건 아니다.**
  - ex) sleep 함수를 사용하게 되면, 싱글스레드에서는 아무런 동작을 하지 못하고 동작을 대기하게 되지만, 멀티스레드에서는 sleep으로 멈춘 경우 다른 스레드로 context switching하여 효율이 개선된다.

- [관련 블로그](https://ssungkang.tistory.com/entry/python-GIL-Global-interpreter-Lock%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)

* * *

## python 멀티 쓰레드와 멀티 프로세스
- 파이썬은 인터프리터 언어로서 기본적으로 싱글 쓰레드에서 순차적으로 동작한다. 따라서 병렬처리(멀티 쓰레드 및 멀티 프로세스)를 하기 위해서는 별도의 모듈을 사용하여 구현해야 한다. 

- **threding 모듈로 멀티쓰레드 구현하기**
  - 파이썬에서 멀티 쓰레드를 구현하는 방법은 threding 모듈(High level)을 사용하거나 thread(Low level) 모듈을 사용하는 것이며, 현재 thread 모듈은 deprecated 되어 **threading 모듈을 사용하는 것을 권장한다.**
  - 먼저 0부터 100,000,000 까지의 합을 구하는 계산 프로그램을 하나의 쓰레드로 동작하게 만들어보자.

```python
from threading import Thread

def work(id, start, end, result):
    total = 0
    for i in range(start, end):
        total += i
    result.append(total)
    return

if __name__ == "__main__":
    START, END = 0, 100000000
    result = list()
    th1 = Thread(target=work, args=(1, START, END, result))
    
    th1.start()
    th1.join()

print(f"Result: {sum(result)}")
```

- 쓰레드는 threading 모듈의 Thread 클래스로 쓰레드 객체를 받아 사용한다. target은 쓰레드가 실행할 함수, args는 그 함수의 인자들을 의미한다. start 함수로 쓰레드를 시작하고 join 함수로 쓰레드가 끝날 때까지 기다린다. 위의 코드의 실행 시간은 약 4.6초가 걸렸다.

- **다음으로 쓰레드를 추가해서 병렬도 동작하는 코드를 만들어보자.**

```python
if __name__ == "__main__":
    START, END = 0, 100000000
    result = list()
    th1 = Thread(target=work, args=(1, START, END//2, result))
    th2 = Thread(target=work, args=(2, END//2, END, result))
    
    th1.start()
    th2.start()
    th1.join()
    th2.join()
```

- th2 를 추가했고, 쓰레드에서 실행되는 함수에 들어가는 인자를 절반씩 나누어 입력하여 따로 계산하도록 했다. 이 코드를 실행하면 하나의 프로세스에서 동작하지만 여러 cpu를 가지고 있다면 쓰레드가 적절히 분산되어 병렬 처리를 할 것이다. 계산 결과는 위와 같았고 실행 시간은 4.6초가 걸렸다.
  - **이전에 하나의 쓰레드로 동작시킨 것과 별반 다르지 않다. 이는 파이썬의 GIL 정책 때문이다.**
  - 위의 코드에서 result 라는 자원을 공유하는 두 개의 쓰레드를 동시에 실행시키지만, 결국 GIL 때문에 한번에 하나의 쓰레드만 계산을 실행하여 실행 시간이 비슷한 것이다.
  - GIL 덕분에 자원 관리(예를 들어 가비지 컬렉팅)를 더 쉽게 구현할 수 있었지만, 지금처럼 멀티 코어가 당연한 시대에서는 조금 아쉬운 것이 사실이다. 그렇다고 파이썬의 쓰레드가 쓸모 없는 것은 아니다. **GIL이 적용되는 것은 cpu 동작에서이고 쓰레드가 cpu 동작을 마치고 I/O 작업을 실행하는 동안에는 다른 쓰레드가 cpu 동작을 동시에 실행할 수 있다. 따라서 cpu 동작이 많지 않고 I/O동작이 더 많은 프로그램에서는 멀티 쓰레드만으로 성능적으로 큰 효과를 얻을 수 있다.**

- **Multiprocessing 모듈로 멀티 프로세스 구현하기**
  - 이러한 상황에서 계산을 병렬로 처리하는데 도움을 주는 것이 바로 **multiprocessing 모듈**이다. multiprocessing 모듈은 쓰레드 대신 프로세스를 만들어 병렬로 동작한다. 
  - 위의 코드에서 Thread 함수가 아닌 Process 함수에서 객체를 받아 사용하는 것과 result로 Queue 객체를 사용하는 것으로 멀티 프로세스를 구현하면 실행시간이 약 2.6초로 현저하게 감소한 것을 확인할 수 있다.
  - 프로세스는 각자가 고유한 메모리 영역을 가지기 때문에 쓰레드에 비하면 메모리 사용이 늘어난다는 단점이 있지만, 이 방식을 통해 싱글 머신 아키텍처로부터 여러 머신을 사용하는 분산 애플리케이션으로 쉽게 전환할 수 있다.
	
<br>

- **결론적으로 말하자면, 파이썬에서 병렬처리를 구현하는 방식은 두가지로 멀티 쓰레드를 사용하거나 멀티 프로세스를 사용하는 것이다.** 쓰레드는 가볍지만 GIL로 인해 계산 처리를 하는 작업은 한번에 하나의 쓰레드에서만 작동하여 cpu 작업이 적고 I/O 작업이 많은 병렬 처리 프로그램에서 효과를 볼 수 있다. 프로세스는 각자가 고유한 메모리 영역을 가지기 때문에 더 많은 메모리를 필요로 하지만, 각각 프로세스에서 병렬로 cpu 작업을 할 수 있고 이를 이용해 여러 머신에서 동작하는 분산 처리 프로그래밍도 구현할 수 있다.
  - **즉, python은 싱글쓰레드에서 동작하지만 멀티쓰레드도 가능은 하다. 근데 GIL때문에 성능적으로 별 차이가 없다. 다만 GIL이 적용되는 것은 CPU 동작에서이고 쓰레드가 CPU 동작을 마치고 I/O 작업을 실행하는 동안에는 다른 쓰레드가 CPU 동작을 동시에 실행할 수 있다. 따라서 cpu 동작이 많지 않고 I/O동작이 더 많은 프로그램에서는 멀티 쓰레드만으로 성능적으로 큰 효과를 얻을 수 있다.**

- [참고 블로그](https://monkey3199.github.io/develop/python/2018/12/04/python-pararrel.html)

* * *

## MRO란
- **MRO는 Method Resolution Order의 약자로, python에서 상속 시 메소드 결정 순서를 의미.**
  - **python은 기본적으로 다중 상속을 지원**하는데, 상속받은 부모 클래스가 서로 겹치지 않는 메소드 이름을 가지고 있다면 문제될 것이 없다.
  - 하지만, 부모 클래스들이 똑같은 이름의 메소드를 가지고 있다면 죽음의 다이아몬드 문제가 발생하게 된다.
    - 죽음의 다이아몬드 문제는, 다중 상속을 받을 때 부모 클래스에 동일한 이름의 메소드를 호출하려 할 때 어떤 부모의 메소드를 호출해야 할지 모르기 때문에 발생하는 문제.
  - 그래서 python은 이 문제를 MRO를 통해 해결한다. **MRO는 자식과 부모 클래스를 전부 포함해서 메소드의 실행 순서를 지정하는 것이다. 따라서 동일한 이름의 메소드가 등장하더라도 지정된 순서대로 실행하면 되니까 문제가 되지 않는다.**

- **MRO 예시**
```python
class Human:
    def say(self):
        print("안녕")

class Mother(Human):
    def say(self):
        print("엄마")

class Father(Human):
    def say(self):
        print("아빠")

class Son(Mother, Father):
    def say(self):
        print("응애")
        
print(Son.__mro__)
>>> (<class '__main__.Son'>, <class '__main__.Mother'>, <class '__main__.Father'>, <class '__main__.Human'>, <class 'object'>)

baby = Son()
baby.say()
>>> 응애
```

- 위의 예시에서는, 최상위 클래스로 Human을 선언하고 Human를 상속받은 Mother, Father 클래스를 선언했다. 그리고 Mother, Father 클래스를 상속받은 Son 클래스를 선언하고 모든 클래스는 say라는 이름의 메소드를 가지고 있다.
- 여기서 모든 것을 상속받은 Son 클래스의 MRO를 확인해보자. **MRO는 \_\_mro\__라는 속성을 통해 확인할 수 있다. 이 속성은 튜플로 되어있다.**
  - 먼저 출력된 값일수록 우선순위가 높다고 볼 수 있다. 즉, Son 클래스 -> Mother 클래스 -> Father 클래스 -> Human 클래스 -> object 클래스 순서로 우선순위를 가지고 있는 것을 확인할 수 있다.
  - **즉, 위의 클래스 순서대로 메소드가 호출된다. Mother와 Father 클래스에서는 먼저 상속받은 순서대로 우선순위가 결정된다. 그리고 최상위 클래스는 object 클래스라는 것을 알 수 있다.**

- 실제로 baby = Son() 이렇게 인스턴스를 만들어서 say 메소드를 실행하면 Son 클래스의 say 메소드가 실행되는 것을 확인할 수 있다.

<br>

- **super 클래스와 MRO**
```python
class Human:
    def say(self):
        print("안녕")

class Mother(Human):
    def say(self):
        super().say() # super 클래스 사용

class Father(Human):
    def say(self):
        print("아빠")

class Son(Mother, Father):
    pass

baby = Son()
baby.say()
>>> 아빠
```

- **위의 예제에서는 Mother 클래스의 메소드가 super 클래스의 say 메소드를 호출하게 되어있다.** 그렇다면 이 때는 Mother 클래스의 부모 클래스인 Human 클래스의 say 메소드가 실행되어 안녕이 출력될 것 같지만 그렇지 않다.
- print(Son.\_\_mro\__) 에서 확인했듯이, Mother 클래스의 다음 우선순위는 Father 클래스이다. **그래서 Mother 클래스의 실제 부모는 Human 클래스가 맞지만, MRO에 의해 Mother 클래스의 super 클래스가 다음 우선순위를 가지는 Father 클래스를 가리키게 된다.**

- [관련 블로그](https://tibetsandfox.tistory.com/26)

* * *

## 변수의 메모리 주소를 확인하는 id 함수
- python은 C언어와 다르게 포인터라는 개념이 없지만 컴퓨터 상에서 돌아가는 언어이기 때문에 내부적으로는 메모리를 사용하고, 이에 대한 주소 정보를 가지고 있다. Python은 메모리 주소를 변수를 구별하기 위한 용도로 사용하고 있다. 
- **python의 built-in 함수 중 id()를 이용하면 현재 확인하고자 하는 변수의 메모리 주소를 확인할 수 있다.**

```python
a = "String"
print(id(a))

4383321904
```

- [관련 블로그](https://technote.kr/289)

* * *

## flag 변수
- flag 변수는 대표성을 가지고 사용하는 변수 이름으로, boolean 타입의 변수로 사용된다.
  - 참, 거짓을 나타내는 것으로 True, False로 표현한다.
- **깃발을 올리고 내리듯이 참과 거짓으로 무언가를 판단할 때 전역변수로 주로 사용한다.**

```python
flag = True
print(flag)
print(type(flag))

if flag:
    print('참 실행')
else:
	print('거짓 실행')
  
  
True
<class 'bool'>
참 실행
```

* * *

## python PEP 8이란
- **PEP 8은 "Style Guide for Python Code"로 파이썬 코드를 어떻게 구상해야할지 알려주는 스타일 가이드이다.**
  - PEP 8은 파이썬 구문의 많은 특수성을 고려하여 작성되었으며, PEP-8은 파이썬 구문을 실제로 만든 파이썬 개발자가 만들었으므로 가장 좋은 표준이라고 할 수 있다.
  - 이러한 PEP 8를 따를 수 있게끔 [포매터](https://github.com/tkdqor/TIL/blob/main/%EA%B8%B0%EC%88%A0%20%EB%A9%B4%EC%A0%91%20%EB%8C%80%EB%B9%84%20%EA%B8%B0%EB%B3%B8%20%EA%B0%9C%EB%85%90%20%EC%A0%95%EB%A6%AC/Distribute.md#formatter%EB%9E%80)나 [린터](https://github.com/tkdqor/TIL/blob/main/%EA%B8%B0%EC%88%A0%20%EB%A9%B4%EC%A0%91%20%EB%8C%80%EB%B9%84%20%EA%B8%B0%EB%B3%B8%20%EA%B0%9C%EB%85%90%20%EC%A0%95%EB%A6%AC/Distribute.md#linter%EB%9E%80)를 사용할 수 있다.

- **PEP 8을 따라야하는 이유**
  - 검색 효율성 : PEP 8에서는 함수의 키워드 인자에 값을 할당할 때는 띄어쓰기를 사용하지 않지만, 변수에 처음 값을 할당할 때는 띄어쓰기를 사용하도록 권고하고 있다. 이를 활용해서 검색 조건을 변경할 수 있다.
  - 일관성 : 코드가 일정한 포맷을 가지면 훨씬 쉽게 읽을 수 있다.
  - 코드 품질 : 코드를 구조화하여 살펴보면 한 눈에 코드를 이해하고 버그와 실수를 쉽게 찾을 수 있다. 좋은 python 코드는 PEP 8과 같은 코드 표준을 준수한다.

- [공식 페이지](https://peps.python.org/pep-0008/)
- [관련 블로그](https://kimjingo.tistory.com/177)

* * *

## args와 kwargs란
- python 함수에 인자를 넣는 방법에는 단순한 방법도 있지만, *args와 **kwargs를 활용할 수도 있다. 
- **매개변수(parameter)는 함수를 정의할 때 변수를 부르는 말이고 인자(argument)는 함수를 호출할 때 매개변수 자리에 넘기는 변수를 의미하는데, args와 kwargs는 함수를 정의할 때 있지 않던 매개변수가 인자로 넘어올 때 어떻게 처리할 것인지 정해주는 역할을 한다.**

- ***args**
  - *args는 arguments의 약자로 동시에 여러 입력값을 주고 싶을 때 사용한다. 사실 * 다음에 꼭 args가 아니어도 elements, argument, ggggg, ksdfkjasdlfkja 등 원하는 변수를 사용하면 된다.
```python
def say_hello(*args):
    for friend in args:
        print('{}야 안녕!'.format(friend))
    print(type(args), args)

say_hello('철수', '영희', '오징어')

철수야 안녕!
영희야 안녕!
오징어야 안녕!
<class 'tuple'> ('철수', '영희', '오징어')
```
- 이렇게 *args를 사용하면 함수 내에서 tuple처럼 사용이 가능하다. 입력값을 굳이 list나 tuple로 주지 않아도 된다.

<br>

- 더 정확하게 말하자면, *args는 함수를 정의할 때 있지 않던 매개변수가 인자로 넘어오게 될 때 tuple로 사용할 수 있도록 해주는 것이다.
```python
def func2(arg1, arg2, *args):
  print(arg1, arg2)
  print(type(args))
  print(len(args))
  print(args)

func2('hello', 123, 'a', 'b', 'c', 1, 2, 3)


hello 123
<class 'tuple'>
6
('a', 'b', 'c', 1, 2, 3)
```

<br>

- ****kwargs**
  - **kwargs는 keyword arguments의 약자로 *args에서 더 나아가 입력값의 키워드를 함께 줄 때 사용한다. 마찬가지로 ** 다음에 꼭 kwargs가 아니어도 원하는 변수를 사용할 수 있다.
```python
def say_hello2(**kwargs):
    for key, friend in kwargs.items():
        if key == 'moon':
            print('내가 좋아하는', end=' ')
        print('{}야 안녕!'.format(friend))
    print(type(kwargs), kwargs)

say_hello2(chul='철수', moon='문어')


철수야 안녕!
내가 좋아하는 문어야 안녕!
<class 'dict'> {'chul': '철수', 'moon': '문어'}
```
- 이렇게 **kwargs를 사용하면 함수 내에서 딕셔너리처럼 사용 가능하다.

<br>

- 더 정확하게 말하자면, **kwargs는 함수를 정의할 때 있지 않던 매개변수가 키워드가 돼서 인자로 넘어오게 될 때 딕셔너리로 사용할 수 있도록 해주는 것이다.
```python
def func4(arg, *args, **kwargs):
  print('arg: {}'.format(arg))
  print( len(args) )
  print(args)
  print( type(kwargs) )
  print(kwargs)

func4(1, 2, 3, some='value', kim='sooran')


arg: 1
2
(2, 3)
<class 'dict'>
{'some': 'value', 'kim': 'sooran'}
```

- 중요한 점은 만약 *args와 **kwargs를 사용할 때는 반드시 순서를 지켜서 넘겨줘야 한다.
  - func4('first', arg='apple', 3, 4, b='bee') 이런 식으로 호출하면 순서에 대한 에러가 나게 된다.

- [관련 블로그](https://lovedh.tistory.com/entry/python-args-kwargs-%EC%B0%A8%EC%9D%B4)
- [관련 블로그2](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=taeil34&logNo=221318004422)
- [관련 블로그3](https://brunch.co.kr/@princox/180#comment)

* * * 

## Pandas란
- pandas는 데이터 조작 및 분석을 위한 Python 프로그래밍 언어용으로 작성된 소프트웨어 라이브러리이다. Python에서 실용적인 실제 데이터 분석을 수행하기 위한 고수준의 객체 형태를 목표로한다. Pandas는 NumPy를 기반으로하며 다른 많은 타사 라이브러리와 잘 통합되도록 설계되었다.

- Pandas는 다음의 종류의 데이터에 적합한 분석 패키지이다.
  - SQL 테이블 또는 Excel 스프레드 시트에서와 같이 이질적으로 유형이 지정된 열이 있는 테이블 형식 데이터
  - 행 및 열 레이블이 포함된 임의의 행렬 데이터 

- [관련 블로그](https://1000yun.tistory.com/2)

* * *

## Numpy란
- NumPy는 행렬이나 일반적으로 대규모 다차원 배열을 쉽게 처리할 수 있도록 지원하는 파이썬의 라이브러리이다. 데이터 분석을 할때, Pandas와 함께 자주 사용하는 도구로 등장한다.
- 실제로 데이터 분석을 수행하기 위한 전제 조건은 컴퓨터가 이해할 수 있도록 데이터를 숫자 형식으로 변환하는 것이다. 여기서 효율적으로 배열을 저장 및 조작할 수 있어야 하는데 이러한 요구사항으로 Numpy 패키지가 나오게 된다.
  - 파이썬의 내장 기능인 리스트 또한 Numpy 배열과 동일한 기능을 제공할 수 있기 때문에 왜 Numpy를 사용해야 하는지 의문이 들 수 있지만, 배열의 크기가 작으면 문제가 되지 않지만 Numpy 배열은 데이터의 크기가 커질수록 저장 및 가공을 하는데 효율성을 보장한다. 이러한 장점으로 파이썬의 Numpy 패키지는 Data Science에 핵심적인 도구로 인식이 되고 있다.

- [관련 블로그](https://brownbears.tistory.com/480)

* * *

## 가비지 컬렉터란
- 가비지 컬렉터라는 개념을 설명하기 앞서 컴퓨터가 일을 할 때, 처리할 정보들을 올려놓을 공간이 필요한데 바로 **메모리**이다. 그리고 코드에 작성하는 변수들, 함수들, 객체들이 메모리 공간을 차지하는 요소들이 된다. 그런데 이 메모리 공간이 점점 꽉 차게 되면 문제가 생긴다. 그래서 중간마다 메모리 공간을 치워줘야 한다.
- C나 C++ 같은 오래된 언어에서는 이러한 메모리 관리를 위해 메모리 할당 및 삭제를 코드로 직접 작성해야 한다. 그런데 잘못 작성하게 되면 메모리 공간이 꽉 차서 컴퓨터가 뻗게된다. 그래서 자바나 파이썬을 비롯한 새로운 언어에서는 **가비지 컬렉터라는 것을 도입하기 시작했다. 즉, 프로그래머가 일일이 하던 메모리 관리인 메모리 할당과 해제를 대신해주는 것으로 사용되지 않는 객체들을 정리해 주면서 메모리 공간을 여유 있게 만들어주는 것이다. 즉, 가비지 컬렉터는 특정 때에 특정 방식으로 필요없는 정보들, garbage들을 쓸어담아다 버려준다.**
  - 이렇게 메모리를 알아서 관리해주는 언어들을 managed language라고 한다. 반대는 unmanaged language라고 부른다.
  - managed language로 직접 메모리를 관리하게 되면 => 필요없는(사용하지 않는) 메모리를 해제해주지 않으면 memory leak이 발생할 수 있고, 사용 중이던 메모리를 해제해버리면 프로그램이 중단되고, 데이터가 손실될 수 있다는 단점이 있다.

- **가비지 컬렉터가 일하는 방식**
  - 가비지 컬렉터가 일하는 방식이 몇가지가 있는데 크게 첫번째로는 **Mark - and - sweep** 방법이다. 메모리를 쭉 훑으면서 아직 필요한 것들만 마크한 다음에 마크가 안된 것들을 갖다 버리는 것이다. 프로그래밍 측면에서 보면 루트에서 닿지 않는 변수들을 치우는 것이다.
  - 또 다른 방법으로는, **Reference Counting**이라는 참조 카운팅 방법이다. 한 요소가 다른 요소에게 몇 번 참조되는지 세어서 그 수가 0이 되면 치우는 것이다.

- **가비지 컬렉터의 한계**
  - 이러한 가비지 컬렉터는 한계가 있다. 사람의 판단력을 갖춘 게 아니기 때문에 지워야 할 것들을 100% 잡아내지는 못한다. 그래서 본인이 managed language를 사용한다고 해서 메모리 관리는 가비지 컬렉터한테 맞기면 된다고 생각하면 안 된다. 좋은 프로그래머가 되려면 메모리 관리에도 신경을 써야 한다. 메모리 누수를 방지하는 방식은 어느 정도 공통적인 것도 있지만 뭘 프로그래밍 하느냐에 따라서 다른 부분들도 있다. 언어나 환경마다 특성이 다르기 때문에 여기에서 누수 방지에 효과적인 방식이 저기에서는 성능만 저하시키기도 할 수 있다. 그래서 우리가 사용하는 언어의 바람직한 메모리 관리 방식을 알아봐야 한다.
  - ex) 대표적인 메모리 관리 방식으로는 **순환참조를 하지 않는 것**이다. 변수들이 서로 참조해버리면 레퍼런스 카운팅이 영원히 0이 되지 않는 문제가 발생하게 된다.

<br>
<hr>

### Python에서의 가비지 컬렉터

- **Python의 Reference Counting**
- 파이썬에서 가비지 컬렉터인 메모리 관리법은 **레퍼런스 카운팅(Reference Counting)이다.** 우리가 객체를 만들 때 그것이 얼마나 자주 사용되는지 카운트하는 것이다. 즉, 파이썬은 우리가 객체를 생성해서 사용할 때마다 해당 객체의 reference count를 1씩 증가시키고, 해제할 때 1씩 감소시킨다. 이것이 0이 되면 객체의 메모리 할당이 해제된다.

- 또한, Python standard library의 **sys 모듈을 사용하여 특정 객체의 reference counts(참조 횟수)를 확인**할 수 있다. 
  - **참조 횟수를 증가시키는 경우는 변수에 객체 할당 / list에 추가하거나 class instance에서 속성으로 추가하는 등의 data structure에 객체 추가 / 객체를 함수의 인수로 전달하는 경우가 있다.**

```python
>>> import sys
>>> a = 'hello'
>>> sys.getrefcount(a)
2
```

- 여기서 참조 횟수가 2인 이유는, 하나는 variable을 생성하는 것이고 두 번째는 변수 a를 sys.getrefcount() 함수에 전달할 때이다.

- 또한, variable을 data structure 각 list 또는 dictionary에 추가하면 참조 횟수가 증가한다.

```python
>>> import sys
>>> a = 'hello'
>>> sys.getrefcount(a)
2
>>> b = [a]
>>> sys.getrefcount(a)
3
>>> c = {'first': a}
>>> sys.getrefcount(a)
4
```

<br>

- **Python의 Generational Garbage Collection**
- Python은 메모리 관리를 위한 reference counting 외에도 **generational garbage collection(세대별 가비지 컬렉션)** 이라는 방법을 사용한다. reference counting이 주로 사용되는 방법이고 보조로 가비지 컬렉션을 사용한다는 것이다.

- 왜 가비지 컬렉션이 필요할까? 전에 객체를 배열이나 객체에 추가하면 참조 횟수가 증가하는 것을 확인할 수 있었다. 그러나 **객체가 자기 자신을 가르키는 것을 의미하는 순환참조**를 진행하면 어떻게 작동 될까?

```python
>>> a = []
>>> a.append(a)
>>> del a
```

- 위와 같은 코드가 있다면, a.append(a)로 a의 참조 횟수는 1이지만 del a로 a가 삭제되었기 때문에 이 객체는 더 이상 접근할 수 없으며 레퍼런스 카운팅 방식으로는 메모리에서 해제될 수 없다.
- **순환참조의 또 다른 예로는 서로를 참조하는 객체가 있을 수 있다.**

```python
>>> a = Func_pr() # 0x01
>>> b = Func_pr() # 0x02
>>> a.x = b # 0x01의 x는 0x02를 가리킨다.
>>> b.x = a # 0x02의 x는 0x01를 가리킨다.
# 0x01의 레퍼런스 카운트는 a와 b.x로 2다.
# 0x02의 레퍼런스 카운트는 b와 a.x로 2다.
>>> del a # 0x01은 1로 감소한다. 0x02는 b와 0x01.x로 2다.
>>> del b # 0x02는 1로 감소한다.
```

- 위와 같은 코드에서는, 마지막 상태에서 0x01.x와 0x02.x가 서로를 참조하고 있기 때문에 레퍼런스 카운트는 둘 다 1이지만 0에 도달할 수 없는 garbage(쓰레기)가 된다. 이러한 유형의 문제를 reference cycle(참조 주기)이라고 하며 reference counting으로 해결할 수 없다.

<br>

- **Python의 Generational Garbage Collector**
- **파이썬의 세대별 가비지 컬렉터에서 generation(세대) 개념에 대해 알아보자.**
  - 가비지 컬렉터는 메모리의 모든 객체를 추적한다. 새로운 객체는 1세대 가비지 수집기에서 life(수명)를 시작한다. Python이 세대에서 가비지 수집 프로세스를 실행하고 객체가 살아남으면, 두 번째 이전 세대로 올라간다. Python 가비지 수집기는 총 3세대이며, 객체는 현재 세대의 가비지 수집 프로세스에서 살아남을 때마다 이전 세대로 이동한다. 

- **파이썬의 세대별 가비지 컬렉터에서 threshold(임계) 값 개념에 대해 알아보자.**
  - 각 세대마다 가비지 컬렉터 모듈에는 임계값 개수의 개체가 있다. 즉, 세대마다 임계값이 정해져 있다. 객체 수가 해당 임계값을 초과하면 가비지 콜렉션이 콜렉션 프로세스를 trigger(추적)하고 해당 프로세스에서 살아남은 객체는 이전 세대로 옮겨지게 된다.
  - **가비지 컬렉터는 내부적으로 generation(세대)과 threshold(임계값)로 가비지 컬렉션 주기와 객체를 관리한다.** 세대는 0~2세대로 구분되고 최근 생성된 객체는 0세대(young)에 들어가고 오래된 객체일수록 2세대(old)로 이동한다. 당연히 한 객체는 단 하나의 세대에만 속한다. 가비지 컬렉터는 0세대일수록 더 자주 가비지 컬렉션을 하도록 설계되어있는데 generational hypothesis에 근거한다. 이 가설은 대부분은 어린 객체가 오래된 객체보다 해제될 가능성이 훨씬 높다는 가설이다.

<br>

- **가비지 컬렉션이 프로그램 성능에 영향을 준다**
  - 가비지 컬렉션을 수행하려면 응용 프로그램을 완전히 중지해야 한다. 그러므로 객체가 많을수록 모든 가비지를 수집하는 데 시간이 오래 걸린다는 것도 분명하다. 가비지 컬렉션 주기가 짧다면 응용 프로그램이 중지되는 상항이 증가하고 반대로 주기가 길어진다면 메모리 공간에 가비지가 많이 쌓일 것이다. 시행착오를 거치며 응용 프로그램의 성능을 끌어 올려야 한다.

- **gc 모듈을 사용하여 가비지 컬렉션 통계를 확인하거나 가비지 컬렉터의 동작을 변경할 수 있다**
  - gc 모듈을 사용하며 get_threshold() method를 사용하여 가비지 컬렉터의 구성된 임계값을 확인할 수 있다.
```python
>>> import gc
>>> gc.get_threshold()
(700, 10, 10)
```

- **각각 threshold 0, threshold 1, threshold 2를 의미하는데 n세대에 객체를 할당한 횟수가 threshold n을 초과하면 가비지 컬렉션이 수행된다.**

- 또한, get_count() method를 사용하여 각 세대의 객체 수를 확인할 수 있다.
```python
>>> import gc
>>> gc.get_count()
(121, 9, 2)
```

- 위 예에서는 youngest generation(가장 어린 세대)에 121개의 객체, 다음 세대에 9개의 객체, oldest generation(가장 오래된 세대)에 2개의 객체가 있다.

- Python은 프로그램을 시작하기 전에 기본적으로 많은 객체를 생성한다. gc.collect() 메소드를 사용하여 수동 가비지 콜렉션 프로세스를 추적할 수 있다.
```python
>>> gc.get_count()
(121, 9, 2)
>>> gc.collect()
54
>>> gc.get_count()
(54, 0, 0)
```

- **위와 같이 가비지 컬렉션 프로세스를 실행하면 0세대에서 67개, 다음 세대에서는 11개 정도의 객체가 정리된다.**
  - 이렇게 수동으로 가비지 컬렉션을 수행할 때는, 고정된 시간 간격마다 호출할 수 있고 / 이벤트가 발생(사용자가 응용 프로그램을 종료하거나 응용 프로그램이 중단)할 때 호출할 수 있다.

<br>

- **Python에서 가비지 컬렉션을 활용하는 방법**
- **Garbage Collector 동작을 수정하지 말기**
  - Python의 주요 장점 중 하나는 개발자의 생산성이 향상하는 것이다. 이유 중 하나는 개발자를 위한 여러 하위 수준의 세부 사항을 자동으로 처리하는 고급 언어이기 때문이다. 수동 메모리 관리는 컴퓨터 자원이 제한된 환경에 더 적합하다. 그러니 garbage collector를 수정하는 것보다 컴퓨터 자원을 증가시키는 편이 훨씬 좋다. Python이 일반적으로 운영 체제 메모리를 다시 release 하지 않는다는 사실을 고려하면 더욱더 그렇다. 메모리를 확보하기 위해 수행하는 수동 가비지 컬렉션 프로세스는 원하지 않는 결과가 나올 수 있다.

- **Garbage Collector 비활성화**
  - Python의 주요 garbage collection mechanism의 reference count는 비활성화할 수 없다. 변경할 수 있는 유일한 가비지 컬렉션 동작은 gc module의 generational garbage collector이다.
  - 세대별 가비지 컬렉션을 변경하는 흥미로운 예 중 하나는 [Instagram에서 가비지 컬렉션을 모두 비활성화](https://luavis.me/python/dismissing-python-garbage-collection-at-instagram) 한 것이다. Instagram 개발팀은 자식 프로세스가 생성된 직후 공유 메모리가 급격히 떨어지는 것을 발견했고, 그래서 모든 세대의 임계값을 0으로 설정하여 가비지 컬렉터 모듈에서 비활성화했다. 이 변경으로 인해 웹 응용 프로그램이 10% 더 효율적으로 실행되었다.
  - 하지만 이러한 경우는 Instagram만의 특별한 케이스이다. 이렇게 Python에서 가비지 컬렉터를 수동으로 관리하려는 경우에는 현재의 문제를 정확하게 파악하는 것이 중요하다.


- [관련 유튜브](https://www.youtube.com/watch?v=24f2-eJAeII)
- [관련 블로그](https://otugi.tistory.com/338), [관련 블로그2](https://medium.com/dmsfordsm/garbage-collection-in-python-777916fd3189)

* * *

## 딕셔너리와 리스트에서 순차적으로 요소를 조회할 때 시간복잡도 비교
<img width="769" alt="image" src="https://user-images.githubusercontent.com/95380638/192290620-7f056f3e-39ff-4c91-932d-4761abe0f923.png">

### 리스트의 경우
- 특정 값에 인덱스로 접근할 경우에는 O(1)
- 순차적으로 요소를 조회하는 for i in arr: 의 경우에는 O(N)
- 특정 인덱스에 값을 삽입하는 arr.insert(i, v) 의 경우에도 O(N)

<br>

<img width="769" alt="image" src="https://user-images.githubusercontent.com/95380638/192291575-e29bf809-c46c-4801-a1b5-40fcfa34cbe7.png">

### 딕셔너리의 경우
- d[k]와 같이 특정 값에 key로 접근하는 경우 O(1)
- d.keys() / d.values()와 같이 key따로 value따로 조회하는 경우에도 O(1) 
- 딕셔너리는 해쉬를 이용해서 고유 인덱스 번호를 저장하기 때문에 key를 알면 바로 번호를 알 수 있어 모든 연산이 O(1)이다. 

<br>

- [관련 블로그](https://infinitt.tistory.com/376)

* * *

## property 사용하기
```python
class Person:
    def __init__(self, first_name, last_name, age):
        self.first_name = first_name
        self.last_name = last_name
        self.age = age

>>> person = Person("John", "Doe", 20)
>>> person.age
20
>>> person.age = person.age + 1
>>> person.age
21
```

- 위와같이 Person 클래스를 정의하고, Person 클래스의 인스턴스를 생성 후에, 현재 필드 값을 읽거나 새로운 필드 값 쓰는 것은 매우 자유롭다.
  - **이렇게 필드명을 사용해서 객체의 내부 데이터에 접근하는 것은 편리하지만, 해당 데이터는 외부로부터 무방비 상태에 놓이게 된다.**

<br>

### Getter/Setter
- 클래스 인스턴스의 내부 데이터를 보호하기 위해서 데이터의 접근용 메서드를 작성하는 것은 객체 지향 프로그래밍에서 흔히 볼 수 있는 패턴이다.
- **일반적으로 데이터를 읽어주는 메서드를 getter(게터), 데이터를 변경해주는 메서드를 setter(세터)라고 한다.**

- Person 클래스에 age 필드에 대한 **get_age()와 set_age() 메서드를 추가해보자. 각각 getter, setter 메서드가 된다.** 나이는 음수가 될 수 없으므로 set_age() 메서드에 음수가 인자로 넘어오면 예외가 발생하도록 한다.

```python
class Person:
    def __init__(self, first_name, last_name, age):
        self.first_name = first_name
        self.last_name = last_name
        self.set_age(age)

    def get_age(self):
        return self._age

    def set_age(self, age):
        if age < 0:
            raise ValueError("Invalid age")
        self._age = age
```

- **여기서 놓치기 쉬운 부분은 \_로 시작하는 이름을 가진 변수는 외부에서 직접 접근하지 않는 파이썬의 관행에 따라, 인스턴스 변수명을 age 대신에 \_age로 변경했다는 점이다. ex) self.\_age = age**

```python
>>> person = Person("John", "Doe", 20)
>>> person.get_age()
20
>>> person.set_age(-1)
ValueError: Invalid age
>>> person.set_age(person.get_age() + 1)
>>> person.get_age()
21
```

- 그리고 이제 위와같이 Person 클래스의 인스턴스에 저장되어 있는 나이 데이터에 접근하거나 변경하려면 메서드를 이용해야 한다.
  - **이렇게 getter/setter 메서드를 통해서 객체의 내부 데이터에 대한 접근을 좀 더 통제**할 수 있게 되었지만 기존에 필드명을 바로 사용할 때 보다는 코드가 조금 지저분해졌다. 뿐만 아니라, Person 클래스의 프로그래밍 인터페이스가 변경됨에 따라 하위 호환성도 깨지게 된다는 큰 단점이 있다.

<br>

## property() 함수와 @property

- **property() 함수**
  - **파이썬의 내장 함수인 property()를 사용하면 마치 필드명을 사용하는 것처럼 깔끔하게 getter/setter 메서드가 호출되게 할 수 있다.**

```python
class Person:
    def __init__(self, first_name, last_name, age):
        self.first_name = first_name
        self.last_name = last_name
        self.age = age

    def get_age(self):
        return self._age

    def set_age(self, age):
        if age < 0:
            raise ValueError("Invalid age")
        self._age = age

    age = property(get_age, set_age)
```

- 이렇게 property() 함수의 첫 번째 인자로 getter 메서드를, 두 번째 인자로 setter 메서드를 넘겨주면 age라는 필드명을 이용해서 다시 나이 데이터에 접근할 수 있게 된다.

```python
>>> person = Person("John", "Doe", 20)
>>> person.age
20
>>> person.age = -1
ValueError: Invalid age
>>> person.age = person.age + 1
>>> person.age
21
```

- 클래스를 사용하는 측면에서는 일반 필드에 접근하는 것처럼 보이지만 내부적으로 getter와 setter 메서드가 호출이 된다. 따라서 나이를 음수로 변경하려고 하면 set_age() 메서드를 직접 호출하는 것과 동일하게 예외가 발생한다.

<br>

- **@property 데코레이터**
  - **파이썬의 내장 데코레이터인 @property를 사용하면 위와 동일하게 작동하는 코드를 좀 더 간결하고 읽기 편하게 작성할 수 있다.**

```python
class Person:
    def __init__(self, first_name, last_name, age):
        self.first_name = first_name
        self.last_name = last_name
        self.age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, age):
        if age < 0:
            raise ValueError("Invalid age")
        self._age = age
```

- **기존의 getter 메서드 위에 @property 데코레이터를 선언하고, 메서드 이름으로부터 get_을 삭제한다. setter 메서드의 경우에는 @<필드명>.setter 데코레이터를 선언하고, 메서드 이름으로부터 set_을 삭제한다.**

```python
>>> person = Person("John", "Doe", 20)
>>> person.age
20
>>> person.age = -1
ValueError: Invalid age
>>> person.age = person.age + 1
>>> person.age
21
```

- **property 함수나 @property 데코레이터를 사용했을 때 가장 큰 이점은 외부에 티 내지 않고 내부적으로 클래스의 필드 접근 방법을 바꿀 수 있다는 것이다.** Person 클래스를 사용하는 관점에서 봤을 때 나이 데이터는 항상 age라는 필드명으로 접근하고 변경할 수 있다는 사실은 변하지 않기 때문이다.

<br>

- **@property 데코레이터 활용**
  - 클래스를 작성하다보면 다른 필드로부터 값이 유추되는 읽기 전용 필드가 필요할 때가 있다. 예를 들어, Person 클래스에서 전체 이름을 얻고 싶다면 다음과 같이 @property 데코레이터를 이용해서 full_name 필드를 추가해줄 수 있다.

```python
class Person:
    def __init__(self, first_name, last_name, age):
        self.first_name = first_name
        self.last_name = last_name
        self.age = age

    @property
    def full_name(self):
        return self.first_name + " " + self.last_name

>>> person = Person("John", "Doe", 20)
>>> person.full_name
'John Doe'
```

- [참고 블로그](https://www.daleseo.com/python-property/)

* * *

## super 함수
- super() 메서드란, 부모의 동작을 불러오는 방법이다.
- 오버라이드 만으로는 충분하지 않고, 부모의 동작은 그대로 하면서 그냥 동작을 끼워넣고 싶을때가 있을 수 있다.
- **self.wave()와 같이 사용하는 건, 객체로 접근해서 정의한 클래스 내부의 메소드나 변수를 사용할 때는 self를 붙인다.**

```python
class Animal():
	def greet(self):
		print(“인사한다”)

class Human(Animal):
	def wave(self):
		print(“손을 흔든다”)	

	def greet(self):
		self.wave()
```

- 이러한 클래스들이 있다고 가정하자. 그러면 Animal의 greet은 “인사한다”인데, Human의 greet은 “손을 흔든다” 이다. 만약에, “인사한다”는 그대로두고 “손을 흔들면서”를 라고만 추가하고 싶다면 어떻게 할까?

```python
class Animal():
	def greet(self):
		print(“인사한다”)

class Human(Animal):
	def wave(self):
		print(“손을 흔들면서”)	

	def greet(self):
		self.wave()
		super().greet()
```

- 그래서 일단 Human 클래스의 wave 메서드를 “손을 흔들면서”라고 수정했다. 
- **그리고 위의 Human 클래스에서 부모 클래스의 greet 메서드를 실행해주고 싶다면, super().greet() 이렇게 해주면 된다.**
  - **즉, super()는 자식 클래스에서 상속받은 부모 클래스의 메서드를 오버라이드하고, 그 부모 메서드를 호출하고 싶을 때 사용한다. ex). super().부모클래스 메서드이름()**

<br>

- **이 동작이 널리 쓰이는 건, 클래스 내부의 특수한 메서드인 \_\_init\__ 메서드일 것이다.**

```python
class Animal():
	def __init__(self, name):
		self.name = name

	def greet(self):
		print(“{}이/가 인사한다”.format(self.name))

class Human(Animal):
	def wave(self):
		print(“손을 흔들면서”)	

	def greet(self):
		self.wave()
		super().greet()

>>> person = Human(“사람”)
>>> person.greet()

손을 흔들면서
사람이/가 인사한다
```

- 이렇게 Animal 클래스에 \_\_init\__ 메서드를 설정하고, name을 입력받아서 자기의 이름으로 저장한다. 그리고 greet 메서드를 format으로 수정해준다.
- 이 상태에서 person = Human(“사람”) 이렇게 Human 인스턴스를 만들때도 init에 name이 들어가 있기 때문에 “사람”이라고 name을 넣어줘야 한다.
  - **즉, 자식 클래스가 부모 클래스를 상속받으면, 부모 클래스의 init 메서드 설정을 그대로 따른다.**

<br>

- **이 상태에서 자식 클래스에도 \_\_init\__ 메서드를 오버라이드해서 사용할 수 있다.**
```python
class Animal():
	def __init__(self, name):
		self.name = name

	def greet(self):
		print(“{}이/가 인사한다”.format(self.name))

class Human(Animal):
	def __init__(self, name, hand):
		super().__init__(name)
		self.hand = hand

	def wave(self):
		print(“{}을 흔들면서”.format(self.hand))	

	def greet(self):
		self.wave()
		super().greet()

>>> person = Human(“사람”, “오른손”)
>>> person.greet()

오른손을 흔들면서
사람이/가 인사한다
```

- 이렇게 Human 클래스에도 \_\_init\__ 메서드를 오버라이드해서 name은 부모 클래스의 \_\_init\__ 메서드를 호출해서 넘겨주고, hand는 Human 클래스에서 정의할 수 있다.
- 이렇게 한 다음, person = Human(“사람”, “오른손”) 이런식으로 인자를 2개 넣어주면 “사람”과 “오른손”이 각각 name과 hand에 들어가고 name은 부모의 \_\_init\__ 메서드가 처리해주고, hand는 Human 클래스에서 처리하게 된다.

<br>

* * *

## classmethod와 staticmethod
- 정적메서드를 지원하는 두 가지 방법이 있다. 바로 **@staticmethod와 @classmethod**이다.
- **먼저 같은 점은 둘 다 인스턴스를 만들지 않아도 class의 메서드를 바로 실행할 수 있게끔 해준다.**
```python
#staticmethod
class hello:
    num = 10

    @staticmethod
    def calc(x):
        return x + 10

print(hello.calc(10))
#결과
20

#classmethod
class hello:
    num = 10

    @classmethod
    def calc(cls, x):
        return x + 10

print(hello.calc(10))
#결과
20
```

- **둘 다 객체를 만들지 않고 바로 해당 메서드를 사용했다. 차이점은 calc메서드를 만들 때 cls라는 인자가 더 추가되었다.**

<br>

- **이제 개념적인 차이점을 살펴보자. 만약 hello 클래스의 num 속성에 접근하려면 어떻게 해야할까? 우선 객체로 접근하는 것이 아니기 때문에 self.num를 사용할 순 없다.** 
- **즉, 클래스 내부에 self.num = 10 이렇게 정의되어 있지 않기 때문이다. 클래스 내부에 num = 10과 같은 num 변수는 클래스 변수로 객체가 생성되고나서 접근이 가능하다. 따라서 self 이런식으로 사용이 불가능하다. 또한, 클래스 변수는 클래스로 만든 모든 객체에 공유된다.**
- 억지로 사용하고 싶다면 @staticmethod는 다음과 같이 해야 한다.

```python
#staticmethod
class hello:
    num = 10

    @staticmethod
    def calc(x):
        return x + 10 + hello.num

print(hello.calc(10))
#결과
30
```

- 위와같이 정적 변수로 접근했다. 반면에 @classmethod는 다르게 접근한다.

```python
#classmethod
class hello:
    num = 10

    @classmethod
    def calc(cls, x):
        return x + 10 + cls.num

print(hello.calc(10))
#결과
30
```

- **classmethod는 cls가 있는데 이것은 '클래스'를 가리킨다. 이것으로 클래스의 어떤 속성에도 접근할 수 있다. 위 예시 경우 또한 cls.num을 통해 hello 클래스의 num 속성에 접근했다.**

<br>

- 만약 상속 관계가 있는 클래스들에선 cls가 가리키는 클래스는 어떤 클래스일까?

```python
class hello:
    t = '내가 상속해 줬어'

    @classmethod
    def calc(cls):
        return cls.t

class hello_2(hello):
    t = '나는 상속 받았어'

print(hello_2.calc())
#결과
나는 상속 받았어
```

- **상속받은 hello_2 클래스가 t 속성을 업데이트 했다. cls.t이 상속시켜준 클래스에 있더라도 이것이 가리키는 것은 상속받은 클래스의 t 속성이다. cls는 상속 받은 클래스에서 먼저 찾는다.**

* * *

## python에서 비동기 코드 작성하기
- 파이썬 3.4에서 **asyncio**가 표준 라이브러리로 추가되고, 파이썬 3.5에서 **async/await** 키워드가 문법으로 채택이 되면서, **파이썬도 이제 언어 자체적으로 비동기 프로그래밍이 가능해졌다.**
- def 키워드로 선언하는 모든 함수는 파이썬에서 기본적으로 동기 방식으로 동작한다. **그런데, 기존 def 키워드 앞에 async 키워드까지 붙이면 이 함수는 비동기 처리되며, 이러한 비동기 함수를 파이썬에서는 코루틴(coroutine)이라고도 부른다.**

```python
async def do_async():
    pass

do_async() # <coroutine object do_async at 0x1038de710>
```

- 이러한 비동기 함수는 일반 동기 함수가 호출하듯이 호출하면 coroutine 객체가 리턴된다.

```python
async def main_async():
    await do_async()
```

- **따라서 비동기 함수는 일반적으로 async로 선언된 다른 비동기 함수 내에서 await 키워드를 붙여서 호출해야 한다.** 이러한 방법으로 전통적인 동기 방식으로 처리하는 과정을 비동기 방식으로 바꿀 수 있다.
  - await키워드가 붙은 함수는 그 함수가 실행이 끝나기 전까지 그 아래의 코드를 실행시키지 않는다. 즉, 동기적으로 사용하는 것처럼 작동하며, await는 async 키워드가 붙은 함수 안에서만 사용이 가능하다.

<br>

- **python 비동기 코드 예시**

```python
import time
import asyncio

async def find_users_async(n):
    for i in range(1, n + 1):
        print(f'{n}명 중 {i}번 째 사용자 조회 중 ...')
        await asyncio.sleep(1)
    print(f'> 총 {n} 명 사용자 비동기 조회 완료!')


async def process_async():
    start = time.time()
    await asyncio.wait([
        find_users_async(3),
        find_users_async(2),
        find_users_async(1),
    ])
    end = time.time()
    print(f'>>> 비동기 처리 총 소요 시간: {end - start}')

if __name__ == '__main__':
    asyncio.run(process_async())
```

- 기존의 함수 선언에 async 키워드를 붙여서 일반 동기 함수가 아닌 비동기 함수(coroutine)로 변경하였으며, time.sleep 함수 대신에 asyncio.sleep 함수를 사용하여 1초의 지연을 발생시킨다. time.sleep 함수는 기다리는 동안 CPU를 그냥 놀리는 반면에, asyncio.sleep 함수는 CPU가 놀지 않고 다른 처리를 할 수 있도록 해준다.
- 주의할 점은 asyncio.sleep 자체도 비동기 함수이기 때문에 호출할 때 반드시 await 키워드를 붙여야 한다는 것이다.

- [참고 블로그](https://www.daleseo.com/python-asyncio/)





