## 1차미션 Basic 해설
- 백엔드 개발자로서 우리는 문제를 항상 받게 되므로, 그 문제를 정의할 수 있어야 한다. 그래서 그 문제에서 입력과 출력을 끄집어낼 수 있어야 한다.

- views.py 예시

```python
import random   # random 패키지 import 하기

def lotto(request):
    lotto_number = list()
    for _ in range(7):     # range(7)하면 -> 0부터 6까지 출력을 하게 된다. 그래서 총 7번 반복하게 된다. 그리고 언더바는 그냥 단순 반복을 의미하는거지, range에서 숫자를 받아서 
                           # 인자를 받아 뽑는 게 아닌 for문이라는 것을 의미한다.
        number = random.randint(1, 45)    # random 패키지의 randint(1, 45)를 사용했는데, 이건 랜덤으로 int를 1부터 45에서 추출한다는 의미이다.
        lotto_number.append(number)       # 그래서 이 number를 리스트 뒤에 붙인다.
    
    return render(request, 'lotto.html', {'lotto_number': lotto_number})
```

- html 예시

```html
<body>
    ...
    <form action="">
        <input type="submit" value="로또 번호 추출하기">
    </form>        
    <br/>
    
    <p>로또 번호 : {{ lotto_number }}</p>
</body>
```

