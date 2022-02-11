## Template
- 웹페이지의 레이아웃을 django에서 Template이라고 부른다. 기본적으로 HTML 코드이고 동시에 원하는 부분에 데이터를 넣어 사용할 수도 있다.
- django template language : python 코드는 아니지만 변수를 활용할 수 있고 사용방식이 유사하다.


## 실습
- 먼저 posts 앱 내부에다가 templates 라는 디렉터리 생성
  - 그리고 그 안에는 앱 이름과 동일한 posts 디렉터리를 추가로 생성. 이 posts 디렉터리 내부에 index.html 생성하기.

- 또한, view index 함수 수정해주기
```python
from django.shortcuts import render

def index(request):
    return render(request, 'posts/index.html')
```    
    
- render 함수를 사용하면 django template를 사용할 수 있게 된다. 첫번째 인자로 HTTP Request 정보인 request를 넣어주고, 두번째 인자는 우리가 사용하고자 하는 template의 경로를 적어주자.
  - 추가로, settings.py에 INSTALLED_APPS에 posts 앱을 추가해주자.

- 이러한 설정을 하고 localhost:8000/posts을 입력하면 index.html이 실행되는 것을 확인할 수 있다.
