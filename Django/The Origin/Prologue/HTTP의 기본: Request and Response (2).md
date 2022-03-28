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
- HttpResponse.status_code의 경우, 상태 코드를 의미한다.
