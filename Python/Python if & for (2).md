## 반복문(for문)
- 반복적인 절차를 처리하기 위한 코드.

```python
stock_list = ['Facebook', 'Apple', 'Amazon', 'Netflix', 'Google']

for stock in stock_list:
    print(f'저는 {stock} 주식을 가지고 있습니다.')

# 저는 Facebook 주식을 가지고 있습니다.
# 저는 Apple 주식을 가지고 있습니다.
# 저는 Amazon 주식을 가지고 있습니다.
# 저는 Netflix 주식을 가지고 있습니다.
# 저는 Google 주식을 가지고 있습니다.  
```

- Python에서는 **for stock in stock_list:** 이렇게 반복문을 사용한다. 그대로 번역해보면 stock_list 안에 있는 stock으로 위에서 리스트로 정의한 주식 목록안에 있는 stock 하나에 대해서 라는 의미이다.
  - 그리고 그 각각 하나씩에 대해서, **print(f'저는 {stock} 주식을 가지고 있습니다.')** 와 같은 코드를 실행한다는 의미이다.

- stock_list에서 순서대로 하나씩 꺼내서 stock이라는 변수에 대입하고, 그 아래 들여쓰기 한 부분에서 print 함수를 반복적으로 수행하게 된다.

- if문과 마찬가지로 for문을 작성하고 콜론(:)을 입력해줘야 python이 어디까지가 for문인지 알 수 있다. 
  - 그 다음줄에는 들여쓰기를 꼭 하고 반복하고 싶은 코드를 작성해주면 된다.


```python
stock_list = ['Facebook', 'Apple', 'Amazon', 'Netflix', 'Google']

for stock in stock_list:
    print(f'저는 {stock} 주식을 가지고 있습니다.')

    print('여기까지가 제가 보유한 주식입니다.')
```

- 이런식으로 print 함수를 for문에 사용된 들여쓰기와 같게해서 추가한다면 -> 위의 print 함수와 동일하게 여러 번 수행이 된다.

- 만약, 계속 반복하지 않고 for문이 끝나고 나서 출력하고 싶다면

```python
stock_list = ['Facebook', 'Apple', 'Amazon', 'Netflix', 'Google']

for stock in stock_list:
    print(f'저는 {stock} 주식을 가지고 있습니다.')

print('여기까지가 제가 보유한 주식입니다.')
```

- 이렇게 들여쓰기를 다르게 해주면 된다.


## For문에서 변수
- for문에서는 **for 변수명 in 변수명** 이렇게 구성되는데, in 다음에 들어와야 하는 데이터는 꼭 리스트일 필요는 없고 **데이터의 나열된 형태나 스퀀스 형태(데이터를 순서대로 하나씩 나열하여 나타낸 데이터 구조 ex. 리스트, 문자열)** 라면 모두 다 사용할 수 있다.

```python
for char in "SANGBAEK":
    print(char)

# S
# A
# N
# G
# B
# A
# E
# K
```    

- "SANGBAEK" 이라는 문자열에 속에 있는 char(charcter의 약자), 즉 글자 하나씩을 뽑아서 출력하게 되는 것이다.


## N번 반복하는 경우
```python
for number in range(9):
    print(f'{number + 1}번째 주식 매수입니다.')

# 1번째 주식 매수입니다.
# 2번째 주식 매수입니다.
# 3번째 주식 매수입니다.
# 4번째 주식 매수입니다.
# 5번째 주식 매수입니다.
# 6번째 주식 매수입니다.
# 7번째 주식 매수입니다.
# 8번째 주식 매수입니다.
# 9번째 주식 매수입니다.    
```

- 다음과 같이 n번 반복을 수행하고자 하는 경우도 있을 것이다. 이런 경우에는 range()라는 함수를 사용해보자.
  - **range(10)** 이라고 하면 -> **0 이상 10 미만의 숫자 범위**를 의미한다. 그래서 0부터 9까지 총 10개의 숫자가 포함되어있는 범위를 의미하게된다. 
  - 그리고 range(10)은 -> **range(0, 10)** 의 단축 표현이다.

