## 1차미션 Challenge 해설

- 입력 페이지 views.py 예시

```python
def lotto_index(request):
    return render(request, 'lotto_index.html')
```

- 입력 페이지 html 예시

```html
...
<form action="/lotto/result/">
      몇개의 게임을 뽑으시겠습니까? <input type="int" name="game" value="1">
      <input type="submit" value="로또 번호 추출하기">
</form>
```

* * *

- 출력 페이지 views.py 예시

```python
def lotto_result(request):
    lotto_number = list()
    game = request.GET.get('game', 1)    # GET 방식으로 전달한 game 데이터를 game이라는 변수에 저장. 없으면 1을 기본적으로 넘겨주기.
    pull_number = [index for index in range(1, 46)]    # python으로 코딩할 때 리스트 안에서 for문을 사용하는 python list comprehension이 있다. 그래서,
                                                       # pull_number안에는 리스트에 1부터 45까지 들어가 있게 된다.
                                                       
    for _ in range(int(game)):                                 # 받은 게임 수만큼 반복
        lotto_number.append(random.sample(pull_number, 6))     # random 패키지를 이용해 pull_number 리스트에서 6개를 샘플을 뽑은 리스트를 -> lotto_number라는 리스트에 append로 추가
        
    return render(request, 'lotto_result.html', {'lotto_number': lotto_number, 'game': game})
```

- 출력 페이지 html 예시

```html
...
<body>
  ...
  <p>게임 수 : {{ game }}</p>
  <p>로또 번호 : {{ lotto_number }}</p>
  <a href="/lotto/">돌아가기</a>
</body>
```



