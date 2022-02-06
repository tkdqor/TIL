## HTTP Basics
- 웹서비스에서 client와 server가 서로 통신할 때는 HTTP 프로토콜을 따른다.
- **GET www.google.com/index.html** 이러한 HTTP Request에 대해서 자세하게 알아보자.
  - 먼저, GET이라는 HTTP method / www.google.com이라는 host / /index.html이라는 요청의 대상
  - HTTP에서는 이렇게 3가지가 가장 중요한 구성요소이다.

### HTTP method
- HTTP method로는, GET / POST / PATCH / DELETE 이렇게 존재한다. client와 server가 통신하는 과정에서 어떤 걸 하고 싶은지 행동을 표현하고자 하는 용도이다. 우리는 이 4가지를 HTTP Verbs라고도 한다.
- GET의 경우에는 server로부터 뭔가를 가져오고 싶을 때, 정보를 요청할 때 사용한다.
- POST는 우편물 발송, 뭔가를 보내는 의미인데 그래서 내가 server에게 어떤 데이터를 전송할 때 사용한다.

3:30
