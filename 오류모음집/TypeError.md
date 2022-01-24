## TypeError
```python
print('=' * 50)

name = input('이름이 무엇인가요? ')
year = input('태어난 해가 어떻게 되시나요? ')
age = 2022 - year + 1

print('=' * 50)
print(f'{name}님, 안녕하세요! 올해 {age}살이 되셨군요?!?')
```

- 다음과 같이 코드를 작성했을 때, 터미널에 TypeError가 뜨게 된다.

```terminal
Traceback (most recent call last):
  File "/Users/kimsangbaek/Desktop/likelion_practice/Python_practice/age_calculator.py", line 5, in <module>
    age = 2022 - year + 1
TypeError: unsupported operand type(s) for -: 'int' and 'str'
```

- Traceback ~~ 은 python을 해석하는 인터프리터가 python 코드를 위에서부터 순서대로 읽으면서 실행을 하고 있다가 어디서 문제, 에러가 발생했는데 어떤 부분에서 에러가 발생했는지를 보여주는 것이다.
- 그 다음엔 우리가 오류를 발생시킨 python 파일의 경로를 나타내고, 몇번째 줄에서 발생했는지도 알려준다.
- TypeError ~~ 는 정수형과 문자열 사이에는 - 연산자를 지원하지 않는다고 나와있다. -> 그래서 지금 year라는 변수가 문자열이라는 의미가 된다. 

- input 함수는 사용자가 어떤 값을 입력하든지 간에 일단은 무조건 문자열로 처리하게끔 구현되어 있기 때문이다. 그래서 사칙연산이 가능한 숫자로 바꿔주기 위해서 int() 라는 함수를 사용하면 된다.

```python
...
age = 2022 - int(year) + 1
```
