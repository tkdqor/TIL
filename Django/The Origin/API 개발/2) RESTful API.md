## RESTful API
- **우리가 백엔드 개발자로서 API서버와 기능들을 개발한다고 했는데, 기능쪽은 python 코드로 만들기 때문에 django 개발과 똑같다고 보면 된다. 자세히 보면, form 대신에 serializer를 쓰는 등 다르지만
  대체적으로 python 기반의 개발을 하기 때문에 비슷하다고 볼 수 있다.**
  
  

## API는 프로그램간의 소통을 위해서 존재
- 예시로, 사람과 사람이 소통하기 위해서는 한국어나 영어 등 언어로 소통을 하게 된다. 
- 비슷하게, 사람이 프로그램과 소통하기 위해서는 python이나 Java 등 프로그래밍 언어로 소통하게 된다. 
- **그럼 마지막으로, 프로그램과 프로그램간 또는 컴퓨터간의 소통을 하기 위해서는 --> 바로 API를 사용해서 소통하게 된다.**
  - 이러한 API는 어떤 기준으로 개발해야 할까? 
  - 우리가 항상 프로그래밍을 할 때 신경써야 하는 건, 개발자간의 협력과 유지보수 및 확장에 용이한 개발을 할 수 있도록 노력해야 한다. 
  - **그랬을 때 필요한 게 바로 "REST API"이다. 즉, API는 REST API와 같은 기준으로 만들어야 한다.**


## RESTful API
- REpresentational State Transfer
- HTTP 통신에서 어떤 자원에 대한 CRUD 요청을 Resource(자원)와 Method(행위)로 표현하여 특정한 형태로 전달하는 방식이다. 
  - 자원은 우리가 만든 POST라는 모델을 의미 / 행위는 우리가 지금까지는 GET과 POST를 사용했었다. 
- 즉, REST 설계 규칙을 잘 지켜서 설계된 API를 RESTful하다라고 표현한다. 


## RESTful API의 특징
- 바탕화면 캡처 참고
- 무상태라는 특징은, 클라이언트가 서버로 요청을 날릴 때, 그 요청을 이해하고 처리할 수 있는 모든 데이터를 요청에다가 실어서 보내줘야 한다. ex) 로그인 시, 서버의 세션과 클라이언트의 세션 id를 확인 
- **캐시 처리 가능**이라는 특징은, 우리가 페이지를 새로고침할 때 이미 존재하는 css나 javascript파일을 새로 요청을 날리지 않고 기존의 있는 요청을 사용하는, 즉 클라이언트는 응답을 캐싱할 수 있어야 한다.
- 계층화라는 특징은, API 서버가 중계 서버라는 점이다. 이 서버가 직접적으로 기능을 처리하지 않을 수 있다. 그래서 Proxy 또는 Gateway가 될 수 있다. 
- 그리고 API 서버로 인해, 클라이언트와 서버를 독립적으로 구분할 수 있다. 그래서 서로간의 의존성을 줄일 수 있다.


## RESTful API 설계 규칙
1. URI는 명사를 사용해야 함
2. 슬래시(/)로 계측 관계를 표현
3. 밑줄(_)을 사용하지 않고 하이픈(-)을 사용
4. URI는 소문자로만 구성
5. HTTP 응답 상태 코드를 사용
6. 파일확장자는 URI에 포함되지 않는다.


## HTTP Method 역할
1. POST : POST를 통해 해당 URI를 요청하면 리소스를 생성
2. GET : GET를 통해 해당 리소스를 조회
3. PUT : PUT를 통해 해당 리소스를 수정
4. PATCH : PATCH를 통해 해당 리소스 부분 수정
5. DELETE : DELETE를 통해 해당 리소스 삭제

- 우리가 지금까지는 form 태그에서 GET/POST 이렇게 2가지 method만 사용한 것은 form 태그에서는 그렇게 2가지만 사용할 수 밖에 없기 때문이었다. 실제 method는 더 다양하다. 
- 그래서 이러한 method를 가지고서, 해당 resource에 GET 요청 또는 POST 요청 등 method에 따라서 --> 서버는 그 resource를 보고 method를 확인해서 요청을 처리해주는 것이다.



## RESTful API 예시
1. URI = {Resource}/ , HTTP Method = POST , CRUD = Create , 비고 = 생성 , ex) posts/ 
2. URI = {Resource}/ , HTTP Method = GET , CRUD = Read , 비고 = 조회(전체 조회) , ex) posts/ 
3. URI = {Resource}/{Identity}/ , HTTP Method = GET , CRUD = Read , 비고 = 조회(부분 조회) , ex) posts/3/
4. URI = {Resource}/{Identity}/ , HTTP Method = PUT , CRUD = Update , 비고 = 전체 수정 , ex) posts/5/
5. URI = {Resource}/{Identity}/ , HTTP Method = PATCH , CRUD = Update , 비고 = 부분 수정 , ex) posts/5/
6. URI = {Resource}/{Identity}/ , HTTP Method = DELETE , CRUD = Delete , 비고 = 삭제 , ex) posts/3/

- 오픈된 API도 이렇게 RESTful하게 작성되어있는 경우가 굉장히 많다. 
- **그렇지만 특정 서비스의 모든 URL의 API가 restful하게 짜여져 있지는 않다. ex) 우리가 로그인/회원가입 경우에는 accounts/login 이렇게 앱이름을 넣는 경우도 있다.**



## URI와 URL
- URI : Uniform Resource Identifier로, 특정 리소스를 식별하는 통합 자원 식별자를 의미. 인터넷에 있는 자원을 나타내는 유일한 주소.
- URL : Uniform Resource Locator로, 흔히 웹 주소라고도 하며 컴퓨터 네트워크 상에서 리소스가 어디 있는지 알려주기 위한 규약 


## URI와 URL 구분
1. www.naver.com -> URI : O / URL : O (URI이면서 URL이다)
2. www.naver.com/posts/ -> URI : O / URL : O
3. www.naver.com/posts/3/ -> URI : O / URL : X (3번처럼 보통 해당 자리는 id나 pk값을 사용하게 되는데, 이 때 이 값 자체는 URL이라고 부르지 않고 URI라고 부른다. 아이덴티티가 들어가기 때문이다.)
4. www.naver.com/posts/?id=3/ -> URI : O / URL : X
5. www.naver.com/static/image/ -> URI : O / URL : O (정적 파일을 불러올 때 image 폴더 안에 있는 것들은 URL이기도 하면서 URI이다)
6. www.naver.com/static/image/logo.png -> URI : O / URL : X  (image 폴더 안에 있는 파일 자체는 URI이지만 URL은 아니다)


## URI 명칭
- http://www.example.com:80/path/to/myfile.html?key1=value1&key2=value2#SomewhereInTheDocument
  - 여기서 http는 프로토콜(https, ssh 등) / www.example.com은 도메인 네임 / 80은 포트 
  - /path/to/myfile.html은 Path to the file / ?key1=value1&key2=value2은 Parameters 또는 쿼리스트링 / #SomewhereInTheDocument은 Anchor(html에 특정 위치로 이동하기 위한 기능을 수행해주는 것)




