## format 함수
- 먼저 format 함수를 사용하는 이유는 문자열 포매팅을 위해서이다.
- 문자열 포매팅은, 문자열 중간 중간에 특정 변수의 값을 넣어주기 위해서 사용하는 것이다.


### format 함수 사용법
- format 함수는 중괄호 {} 안에 포매팅을 지정하고 format함수의 인자로 값들을 넣어준다.

```python
# 직접 대입
s1 = 'name : {}'.format('BlockDMASK')
print(s1)

# 변수로 대입
age = 29
s2 = 'name : {}'.format(age)
print(s2)

s3 = 'number : {num}, gender : {gen}'.format(num=123, gen='남')
print(s3)
```

- 이렇게 format 함수 다음에 문자열을 직접 입력해도 되고 변수를 따로 지정해서 넣어도 된다.

```python
s4 = 'song1 : {}, song2 : {}'.format('nunu', 'nana')
print(s4)

s5 = 'song1 : {1}, song2 : {0}'.format('nunu', 'nana')
print(s5)
```

- 또한, 다음과 같이 중괄호 {} 안에 아무것도 입력하지 않으면 -> format 인자 순서대로 들어가게 된다.
- 그리고 s5처럼 중괄호 {} 안에 인덱스를 반대로 입력하면 -> 번호에 맞는 format 인자가 들어가게 된다.

* * *
- 관련 블로그 : https://blockdmask.tistory.com/424
