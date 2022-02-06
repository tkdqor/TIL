## HTTP Basics
- 웹서비스에서 client와 server가 서로 통신할 때는 HTTP 프로토콜을 따른다.
- **GET www.google.com/index.html** 이러한 HTTP Request에 대해서 자세하게 알아보자.
  - 먼저, GET이라는 HTTP method / www.google.com이라는 host / /index.html이라는 요청의 대상
  - HTTP에서는 이렇게 3가지가 가장 중요한 구성요소이다.


### HTTP method
- HTTP method로는, GET / POST / PATCH / DELETE 이렇게 존재한다. client와 server가 통신하는 과정에서 어떤 걸 하고 싶은지 행동을 표현하고자 하는 용도이다. 우리는 이 4가지를 HTTP Verbs라고도 한다.
- GET의 경우에는 server로부터 뭔가를 가져오고 싶을 때, 정보를 요청할 때 사용한다.
- POST는 우편물 발송, 뭔가를 보내는 의미인데 그래서 내가 server에게 어떤 데이터를 전송할 때 사용한다.
  - 새로운 글을 쓰거나, 리뷰를 작성하는 등 client가 작성한 데이터가 server로 전달되어야 하는 경우이다.
- PATCH는 게임에서 기존 내용이 수정되고 업데이트 될 때 사용하는 단어인데, 내가 server에 있는 데이터를 수정하고 싶을 때 사용한다.
- DELETE는 server에 있는 데이터를 삭제하고 싶을 때 사용한다.

-> HTML form element는 GET, POST만 사용가능하다.
-> **우리가 브라우저에서 www.google.com 이렇게 입력하는 것은 GET 방식으로 요청하는 것과 같다. 또한, a element 역시 GET 방식이다.**


### Host
- HTTP Request에는 그 요청을 누구에게 보낼지, 정확하게는 네트워크 상의 어떤 server에게 요청을 보낼지를 지정해야 한다. 그러기 위해서 네트워크 상의 수많은 컴퓨터 중에서 대상 컴퓨터의 주소, host 주소를 적게 된다.
  - 꼭 도메인일 필요는 없고 IP주소여도 상관없다.
- server를 특정할 수 있는 도메인이나 IP 주소가 필요하다.
- **GET www.google.com** -> 여기까지만 보면, client가 구글 server로부터 뭔가를 GET, 데이터를 얻고 싶다는 것이다.


### Host 주소 뒤에 붙는 HTTP Request URL
- 해당 HTTP GET Request가 요청하고자 하는 실질적인 대상을 의미한다. server 입장에서는 Requested URL이라고 한다.
  - **URL은 문자 그대로 server로부터 우리가 원하는 resource를 지칭하기 위한 주소이다. 그래서 django의 URL configuration 개념이 나오게 된다.**

- **GET www.google.com/index.html** -> 그래서 이 말은, **우리가 www.google.com이라는 구글 server로부터 index.html이라는 resource를 원한다는 것이다.**

- 과거에는 이 URL를 적을 때 확장자를 포함한 파일의 이름을 적어주었는데, 지금은 꼭 파일의 이름일 필요는 없고 단순히 경로로 작성해도 된다.
  - 그리고 우리가 일반적으로 www.google.com처럼 /를 빼고 도메인만 입력하면 -> 해당 도메인의 루트, /로 표현된 경로로 데이터를 요청하게 된다.
  - 그래서 우리가 www.google.com이라고 입력해도 실제로는 -> www.google.com/ 이걸 의미한다.
  - **또한, 대부분의 웹사이트는 본인 웹사이트의 메인페이지를 index.html이라는 파일로 만들어두고 그냥 도메인으로만 request가 왔을 때, 기본 페이지의 index.html을 서빙하게끔 개발해두었다.**

- 모든 Request URL의 경로는 처음 시작을 /를 적는 것으로 시작하기 때문에, 도메인을 적고 /만 적은 상태(www.google.com/)에서 마무리를 하게 되면 모든 경로의 시작이니까 이 도메인이 가져야 할 가장 기본적인 웹 페이지, 메인페이지 resource가 바로 도메인 주소 다음에 /만 적었을 때 서빙이 되게 한다. 
  - 우리가 /를 지우더라도 /를 붙인 것과 동일한 효과가 난다.

