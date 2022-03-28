## HTTP의 기본: Request and Response (1)


### HTTP
- **컴퓨터가 서로 데이터를 주고 받는 방법**
- 사용자가 브라우저를 통해서 server로 요청을 날리고 server는 그 요청을 처리하고 응답하는 과정에서 --> 브라우저와 서버 사이에 HTTP 요청과 HTTP 응답이 서로 주고받게 된다. 이걸 각각 Request와 Response라고 한다.
- 우리가 Views.py에서 요청을 처리하는 함수를 정의할 때 첫번째 인자가 Request였다. 그 Request가 바로 이 Request을 의미한다.
  - **즉, 브라우저가 HTTP 요청을 server에게 하게되고 / Server가 브라우저에게 HTTP 응답을 하게 된다.**

- **django 실행 흐름을 생각해보면 --> 처음 웹 브라우저가 HTTP 요청을 하게 되면 -> django의 urls.py가 받게 되고 View한테 넘겨준다. 그리고 나중에 View가 HTTP 응답을 하게 된다. 이게 제일 핵심적인 부분이다.**
  - 브라우저가 HTTP 요청을 보내면 브라우저가 보내는 모든 정보가 Request에 담겨있다. 보내는 데이터, URL, 시간, 위치 등이 다 담겨있다. 

- **그래서 HTTP는 Hyper Text Transfer Protocol로 HTML 문서와 같은 리소스들을 가져올 수 있도록 해주는 프로로콜, 약속이다.**
- HTTP Request가 될 때 담겨지는 정보들은 다음과 같다.
  - HTTP method인 GET, POST, PUT, PATCH가 담겨 있고, 어떤 컴퓨터의 요청인지 HOST 정보도 담겨있다. 이건 start-line에 담겨있다.
  - 그리고 User-agent라는 어떤 브라우저를 사용했는지에 대한 정보도 있다.
  - Accept은 어떤 데이터인지 알려준다. text나 html 등등.
  - **Accept-Language가 있는데, 이렇게 Request에서 날라오는 Accept-Language를 보고 django는 해당 언어에 맞게 Response를 해줄 수 있다.** 0.5는 가중치를 의미.
    - **그래서 위와같은 정보들이 HTTP Header에 담기게 된다.**

- **HTTP는 구성이 존재한다.**
  - **처음에 start-line이 있고 / HTTP headers가 있고 / empty line라는 빈 공간이 있고 / 맨 밑에 body가 있다.**
  - start-line에는 method와 버전이 들어간다. 
  - body에는 실질적인 데이터가 들어가는건데, 우리가 Html로 응답을 해줄 때 이게 body에 들어가게 된다.

- **HTTP Resonse를 줄 때 담겨지는 내용들도 다르다.**
  - **start-line에 3자리의 상태코드가 담겨진다. 200대는 요청에 대한 제대로된 응답이라는 의미이다. 300대는 요청에 대해서 redirection를 시켜주는 것이다. 400대는 클라이언트 에러가 발생한 상태값이다. 
    500대는 server 에러를 의미한다.**
  - HTTP Headers에는 Content-Type이라고 해서 html을 응답해줄 때 html이라고 적혀있게 된다.

* * *
- **브라우저 화면에서 개발자 도구를 켜서 네트워크를 클릭해보자.** 그리고 새로고침을 하게되면, 새로운 항목들이 뜨게 되고 --> 그 중에서도 type이 document라고 되어있는 Name을 클릭해보자.
  - 그러면 또 자세하게 여러 항목들을 볼 수 있는데, 그 중에서도 **Headers**를 클릭해보면 **HTTP 요청 헤더값과 응답 헤더값이 실리게 된다.** 이렇게 브라우저의 개발자도구를 통해 HTTP Request와
    Response에 어떤 데이터가 담겨지는지 확인할 수 있다.
    
- 네이버를 들어가서 해보면 더 많은 것들이 뜨게 된다. 즉, 네이버 메인 페이지 하나를 띄우기 위해서 이렇게 많은 통신들을 하게 되는 것이다. 이번에도 맨 위에있는 document를 클릭하고 headers의 
  Request Headers를 보면, 도메인이 있고 method도 있고 스키마라고 해서 Https가 있고 accept-language도 있다. General 부분에는 상태 코드 200도 볼 수 있다.
