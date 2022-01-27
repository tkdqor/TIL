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
