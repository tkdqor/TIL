# Python


📖 **Contents**

- Python
  - [문법 관련 내용](#문법-관련-내용)
  - [Python으로 개발공부를 시작한 이유](#python으로-개발공부를-시작한-이유)
  - [Python의 장점과 단점](#python의-장점과-단점)
  - [assert란](#assert란)
  - [append와 extend 차이](#append와-extend-차이)
  - [리스트에서 [-1]이란](#리스트에서--1이란)
  - [Python은 인터프리터 언어](#python은-인터프리터-언어)
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
  - [클래스 메서드와 static 메서드](#클래스-메서드와-static-메서드)
  - [python 관련 블로그](#python-관련-블로그)
  - [python 관련 블로그2](#python-관련-블로그2)
  - [python으로 파일 읽고 쓰기](#python으로-파일-읽고-쓰기) 
  - [매직 메소드](#매직-메소드)
  - [\_\_class\__ 변수](#던더class던더-변수)
  - [\_\_new\__ 변수와 \_\_init\__ 함수의 차이](#던더new던더-변수와-던더init던더-함수의-차이)
  - [변수가 있는지 확인하는 hasattr](#변수가-있는지-확인하는-hasattr)
  - [python에서 상수 처리 하기](#python에서-상수-처리-하기)
  - [GIL이란](#gil이란)
  - [MRO란](#mro란)
  - [변수의 메모리 주소를 확인하는 id 함수](#변수의-메모리-주소를-확인하는-id-함수)

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
- 장점 : python은 문법이 다른 언어에 비해서 간결하게 구성되어 있다. 그래서 이러한 이유로 오류 발생을 줄일 수 있고, 조금 더 빠르게 개발을 진행해나갈 수 있다는 장점이 있다. 또한 python을 기반으로 django나 fastAPI, Flask와 같은 다양한 웹 프레임워크를 사용할 수 있다는 점도 큰 장점이다. 그리고 python은 다양한 라이브러리가 존재하여 많은 분야에 사용될 수 있다.

- 단점 : python은 컴파일 언어가 아닌 인터프리터에 의해 실행되는 스크립트 언어이기 때문에 실행할 때마다 코드를 한 라인씩 바로 기계어로 변환하는 과정을 거쳐야 한다. 그래서 실행속도가 다소 느린 점이 단점이라고 볼 수 있다.

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

## Python은 인터프리터 언어
- **Python은 컴파일러(compiler)를 사용하는 C언어나 자바와는 달리 인터프리터에 의해 실행되는 스크립트 언어이다.**
- 인터프리터(interpreter)란 소스 코드를 처음부터 한 라인씩 차례대로 해석하며 실행하는 프로그램을 의미한다.
  - 인터프리터는 실행할 때마다 코드를 한 라인씩 바로 기계어로 변환하기 때문에 실행 속도가 컴파일러 언어에 비해 다소 느리지만, 코드가 완전히 작성되지 않아도 작성된 부분까지만을 테스트할 수 있다.

* * *

## Flask와 Django 그리고 FastAPI
- python framework 중 **Django**는 웹 서비스 호스팅용으로 사용하기 적합하며 CRUD를 수행하기 위한 최적의 프레임워크이다. 다만 빠른 웹 프레임워크는 아니어서 무겁다는 단점이 있다.
  - Django는 한 프로젝트 내에 다양한 어플리케이션이 존재 할 수 있다. 그리고 Django는 ORM이 있어 DB에 접근하기에 편리하다. 또한, 이미 많은 것이 개발되어 있어 내가 원하는 기능을 유연하게 추가할 수 없다.
- python framework 중 **Flask**는 micro framework이기에 Django보다 가볍고, 개발 속도가 매우 빠르다는 장점이 있다. 다만 개발자가 많은 것을 설정하고 세팅해야 한다.
  - Flask는 프로젝트마다 1개의 어플리케이션을 개발하도록 되어있다. 그리고 따로 ORM이 없기 때문에 DB에 접근할 때 SQLAlchemy 등을 사용해야 한다. 또한, 확장과 커스터마이징이 편리하다.
 
- 최근에는 **fast API**라는 framework도 존재한다. 
  - django와 다르게 비동기 통신을 지원하고 빠르다는 장점이 있다. 그리고 django의 경우 프로젝트 및 앱을 생성하면 알아서 디렉터리가 생성되고 디자인 패턴이 적용되지만, FastAPI의 경우에는 main.py에서 시작해서 아무런 구성이 되어있지 않아서 개발자가 많은 것을 커스텀해야 한다.

* * *

## 리스트와 튜플 그리고 딕셔너리의 차이
- 리스트 : 대괄호 표시를 사용하고 element(요소)의 수정, 삽입, 삭제가 가능
- 튜플 : 소괄호 표시를 사용하고 element의 변경이 불가능, 순서는 변경 가능. 리스트보다 메모리를 더 적게쓰고 빠르다.
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
- self는 객체의 인스턴스 그 자체를 말한다. 즉, 객체 자기 자신을 참조하는 매개변수 역할을 한다. 객체지향 언어는 모두 이걸 메소드에 안보이게 전달하지만, 파이썬은 클래스의 메소드를 정의할 때 self를 명시한다. 그래서 메소드를 불러올 때 self는 자동으로 전달된다. self를 사용함으로 클래스내에 정의한 멤버에 접근할 수 있게된다.
  - **그래서 클래스 내부의 변수를 정할 때는 -> self.변수명 / 클래스 내부의 메소드를 정할 때도 def 메소드명(self):**
  - 반면에 함수 내부의 변수는 self를 붙이지 않고 변수명을 바로 입력한다.

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

- 텍스트를 바로 번역하는 것이 아니고 해당값이 번역되어야할때 함수가 동작한다.

* * *

## 클래스 메서드와 static 메서드
- **클래스 메서드**는 매개변수로 self가 있는 경우이다. 그래서 클래스에 접근할 수 있게 된다.
- 반면 **static 메서드**는 매개변수로 self를 두지 않는다. 그리고 클래스가 접근하지 못하게 되어 개체의 속성을 바꾸지 못하게 하고 싶을 때 사용할 수 있다.


* * *

## python 관련 블로그
- https://han.gl/JaNWX


* * *

## python 관련 블로그2
- https://uiandwe.tistory.com/1272?category=923644


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

- **파일 쓰기 write, writeline, writelines 함수**
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

## 던더class던더 변수
- 특정 객체의 정의 되어있는 상위 클래스를 의미.

```python
...
if obj.__class__ == get_user_model():
...

```

* * *

## 던더new던더 변수와 던더init던더 함수의 차이
- \_\_new\__ 변수는 클래스 객체가 생성되면 먼저 실행 생성자라서 객체의 메모리 할당을 진행하는 객체 속성이라고 할 수 있다.
- \_\_init\__ 함수는 클래스 생성자로 초기 셋팅을 진행한다. 생성자는 어떤 클래스의 인스턴스(객체)가 생성될 때 파이썬 인터프리터에 의해 자동으로 호출되는 메소드이다.

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

* * *

## GIL이란
- **GIL이란, Global Interpreter Lock의 약자로 파이썬 인터프리터가 한 스레드만 하나의 바이트코드를 실행시킬 수 있도록 해주는 Lock을 의미한다. 하나의 스레드에 모든 자원을 허락하고 그 후에는 Lock을 걸어 다른 스레드는 실행할 수 없게 막아버리는 것이다.**
  - 그래서 python에서 여러개의 스레드를 통해 작업하는 경우, 병렬적으로 작업을 하는 것이 아니라 각각의 스레드는 GIL를 얻고 동작하게 되고 이 때 다른 스레드는 모두 동작을 멈추게 된다. 
  - 그리고 멀티스레드의 경우, thread context switch에 따른 비용도 발생하기 때문에 오히려 싱글스레드보다 시간이 오래 걸리는 문제가 발생한다.

- **python에서 GIL를 사용하는 이유**
  - python의 모든 객체는 reference count라고 해서 해당 변수가 참조된 수를 저장하고 있다. 
  - 그래서 멀티스레드인 경우에는 여러 스레드가 하나의 객체를 사용한다면, reference count를 관리하기 위해서 모든 객체에 대한 lock이 필요하다.
  - 이러한 비효율을 막기 위해 python에서 GIL를 사용하게 되었다. 하나의 lock를 통해 모든 객체들에 대한 refernce count의 동기화 문제를 해결한 것이다.

- **다만, python에서 무조건 멀티스레드가 싱글스레드보다 느린 건 아니다.**
  - ex) sleep 함수를 사용하게 되면, 싱글스레드에서는 아무런 동작을 하지 못하고 동작을 대기하게 되지만, 멀티스레드에서는 sleep으로 멈춘 경우 다른 스레드로 context switching하여 효율이 개선된다.

- [관련 블로그](https://ssungkang.tistory.com/entry/python-GIL-Global-interpreter-Lock%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)

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
