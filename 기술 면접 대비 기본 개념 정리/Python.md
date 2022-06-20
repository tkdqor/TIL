# Python


📖 **Contents**

- Python
  - [append와 extend 차이](#append와-extend-차이)
  - [리스트에서 [-1]이란](#리스트에서--1이란)
  - [Python은 인터프리터 언어](#python은-인터프리터-언어)
  - [Flask와 Django](#flask와-django)
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

## python 관련 블로그
- https://han.gl/JaNWX


* * *

## python 관련 블로그2
- https://uiandwe.tistory.com/1272?category=923644




