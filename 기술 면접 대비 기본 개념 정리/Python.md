# Python


📖 **Contents**

- Python
  - [append와 extend 차이](#append와-extend-차이)
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

## python 관련 블로그
- https://han.gl/JaNWX


* * *

## python 관련 블로그2
- https://uiandwe.tistory.com/1272?category=923644




