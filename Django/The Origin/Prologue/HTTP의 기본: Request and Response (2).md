## HTTP의 기본: Request and Response (2)
- django에서 HTTP를 쓰기 위해서 Request와 Response에 대해서 클래스가 제공이 된다.
- 관련 공식문서를 참고하자. https://docs.djangoproject.com/en/4.0/ref/request-response/
  - **공식문서 화면에서 우클릭하고 "한국어로 번역"을 누르면 한국말로 볼 수 있다.** 처음부터 끝까지 한 번 읽어보자.


### HttpRequest 클래스
- HttpRequest클래스의 속성도 나와있다. 우리가 사용할 수 있는 값들이다. 
  - HttpRequest.path같은 경우 어떤 주소로 요청을 날렸는지 주소를 볼 수 있다.
  - HttpRequest.GET은 지난번에도 사용해봤는데, GET 요청으로 오는 값들을 받을 수 있는 속성이라는 의미이다. HttpRequest.POST도 사용가능하다. POST 요청으로 들어오는 값들을 받는 것이다. GET은 
    리소스를 요청할 때 클라이언트가 서버에 보내는 방식이고, POST는 리소스를 수정, 즉 데이터를 생성, 수정, 삭제등을 할 때 POST로 보내게 된다.
  - HttpRequest.META에서 REMOTE_ADDR는 클라이언트의 IP 주소가 담겨있다.(없을수도 있음) 또는 REMOTE_USER 이렇게 서버에서 인증된 사용자인지도 확인할 수 있다.
  - HttpRequest.headers는 User-Agent라고 있는데, 클라이언트가 접속한 agent를 의미하는 것이다. 그래서 Mac OS로 들어왔다 이런 게 담겨져 있다. --> 해당 부분은 우리가 개발자도구를 키고 네트워크 - document를 클릭해서 header부분을 봐도 나와있다. 그러면 크롬이라고도 나와있다.
  - HttpRequest.user도 많이 사용된다.
    - **원래 HTTP에 들어있는 건 아니지만, 미들웨어가 설정한 속성이라는 게 있다. 미들웨어는 사실 urls.py 앞에 있는 부분이다. 그래서, 웹 브라우저가 요청했을 때 urls.py가 아닌 미들웨어가 가장 처음으로 해당 요청을 받게 된다.** 이 미들웨어에서 여러가지 작업을 해준다. 그 중에 하나가 바로 **HTTP 요청을 날린 사람이 로그인된 사용자인지를 알 수 있는 user라는 걸 넣어주는 것이다.** 그래서 django내에서 인증을 해야할 때 많이 사용된다. 



### HttpResponse 클래스
- HttpResponse.status_code의 경우, 상태 코드를 의미한다. 공식문서에서 각 코드가 어떤 의미인지 알려주고 있다.
- 주로 우리가 데이터를 Response로 진행하기 때문에 속성들을 많이 활용하지는 않는다.



### 공식문서 말고도 다른 방식으로 보는 방법
- **다시 우리의 VSCode를 보면, View에 있는 request가 바로 --> HttpRequest 클래스로 만든 객체를 의미한다.**
- 그래서 이 request라는 객체가 어떤 요소를 가지고 있어서 우리가 사용할 수 있는지 궁금하다면 print(request) 이렇게 터미널에 출력을 해봐도 된다.

```python
def calculator(request):
    print(f'request = {request}')
    print(f'request type = {type(request)}')
...
```

- 이렇게 터미널에 출력해보면, 

```terminal
request = <WSGIRequest: GET '/calculator/'>
request type = <class 'django.core.handlers.wsgi.WSGIRequest'>
```

- **이렇게 출력되는 것을 볼 수 있고, 여기에 WSGIRequest를 View에서 from django.core.handlers.wsgi import WSGIRequest 이렇게 import하고 command로 클릭해서 보면, 결국에는 WSGIRequest도 HttpRequest를 상속받았다는 것을 확인할 수 있다.**
- **상속받는다는 것은, 부모 클래스를 자식 클래스가 변수,상수,함수 이런것들을 다 내려받는다는 것이다. 그래서 결국은 --> Request가 HttpRequest라는 의미이다.**


```python
print(f'request.__dict__ = {request.__dict__}')
```

- 한가지 더 print할 수 있는 방법은, 위와같이 request를 .__dict__으로 찍는 것이다. 즉, 접근할 수 있는 속성들을 모두 보여준다는 의미이다. 이렇게하고 새로고침을 하면 터미널에 엄청 많은 데이터들이 출력된다.

```terminal
request.__dict__ = {'environ': {'...': '...', 'HTTP_USER_AGENT': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.83 Safari/537.36',...}
```

- 여기에는 우리가 아까 봤던 HTTP_USER_AGENT도 있다. 이렇게 데이터들을 확인해볼 수 있다.

* * *
- 또 다른 디버깅 방법은, VSCode에서 **중단점**을 찍는 것이다. 파일 화면에서 내가 원하는 코드 위치가 있다면, 이 코드가 실행되기 전에 잠깐 멈춰서 뭔가를 보고싶다라고 했을 때, 그 코드 라인 옆에 빨간색의 중단점을 찍어준다.
<img width="408" alt="image" src="https://user-images.githubusercontent.com/95380638/160336948-fbbbf4f9-6138-46f9-9175-7ff931671b5a.png">

- 그리고나서 VSCode 좌측 네번째 버튼인 **실행 및 디버그** 버튼이 있다. 여기를 클릭해준다. 그러면 launch.json 파일을 만들라고 하니까 클릭한다. 그러면 Select a debug configuration이 나오는데, 여기서 Django를 클릭하자. 

<img width="229" alt="image" src="https://user-images.githubusercontent.com/95380638/160337507-9c881ae7-d330-4250-bce5-10dc296a64fa.png">

- 그러면 최상위 디렉터리 위치에 .vscode가 생기고 그 안에 launch.json이라는 파일이 하나 생성된다.
  - 이 
파일

