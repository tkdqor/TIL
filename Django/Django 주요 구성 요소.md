## django 주요 기능들
- Function Based Views : 함수의 형태로 HTTP 요청 처리. 매 요청이 올 때 마다 특정 함수가 views.py에서 실행된다.
- Class Based Views : 클래스를 기반으로하는 함수 기반 view 만들기.
- Models : 데이터베이스의 인터페이스를 담당하는 ORM 역할
- Templates : 복잡한 문자열 조합을 보다 용이하게 할 수 있도록 도와줌. 주로 HTML 문자열 조합으로 많이 사용하나 푸쉬 메세지나 이메일 내용을 만들때에도 쓰면 편리.
- admin 기초 : django에서 심플한 데이터베이스 레코드 관리 UI를 제공해줌.
- Logging : 다양한 경로로 메시지 로깅
- Static Files : 개발 목적으로의 정적인 파일 관리
- Messages Framework : 유저에게 1회성 메시지 노출 목적. ex) 어떤 걸 저장하고나서 "저장했습니다", 삭제하고나서 "삭제했습니다" 
- Forms : 유저가 사용할 수 있는 입력폼 생성, 입력값 유효성 검사 및 DB로의 저장 / django를 쓸 줄 안다면 Forms를 적극 활용해야 한다.(추가로 serializer도 잘 활용하면 django에서의 개발 생산성을 극대화 시킬 수 있음) 
- 테스팅 / 국제화&지역화 / 캐싱 지원
- Geographic : DB의 Geo 기능 활용(Postgre SQL 중심)
- Sending Email & Syndication Feeds(RSS /Atom) / Sitemaps 지원


## django 기본 앱
- django 기본 앱은 env -> lib -> python3.9 -> site-packages -> django -> contrib라는 디렉터리에 모두 위치해 있다.
- admin / admindocs / auth / contenttypes / flatpages / gis / humanize / messages / postgres / redirects / sessions / sitemaps / sites / staticfiles / sydication
  - app이라는 것은 django가 규정해놓은 하나의 python 패키지라고 보면 된다.
  - admin은 관리자 / auth는 인증 / messages는 mesages Framework / static files는 정적 파일 관리 및 처리
  - 이러한 기능들 덕분에 우리가 django로 웹 개발을 더 수월하게 할 수 있다.


## 웹 애플리케이션 기본 구조
- 웹 브라우저(클라이언트)가 어떤 서버로 항상 먼저 요청을 보내게 되고 <-> 다양한 언어/프레임워크로 만드는 웹서버(우리는 django)에서 그에 따른 로직이 수행되고 그 로직에 맞춰 <-> 데이터베이스 서버(SQLite, MySQL, PostgreSQL 등)에서 데이터를 가져오거나 데이터를 넣거나 수정 및 삭제하는 것이다.      
                                                                                                                                      <-> 또한 웹서버에서 적절하게 캐시 서버(Memcached, Redis)에 세팅, 업데이트, 삭제를 하고 값도 가져오기도 함.    
  이렇게 값도 가져오고 응답을 만들어서 웹 서버가 다시 웹 브라우저(클라이언트)에게 돌려주는 것이다. 
  
- 그 응답을 HTMl 포맷일 수도 있고 혹은 엑셀 포맷일 수도 있고 pdf 응답, 재순응답 등 다양하게 있을 수 있다.
  - 우리가 문서를 만들 때, 워드로도 만들 수 있고 한글로 만들 수 있고 엑셀로도 만들수 있듯이 -> 서버단에서는 클라이언트가 원하는 형식이 어떠한 포맷이든 만들 수 있다.   


## Django 기본 구조
- 위의 기본 구조중에서도, 웹서버를 Django로 만든다면 어떻게 구성할 수 있을까.
1) URL Conf : 클라이언트가 요청을 하게 되면, URL Conf에서 미리 URL별로 호출할 함수를 리스트에 등록한다. 
2) View : 요청한 URL에 맞춰서 어떤 함수가 호출이 됨. 그 함수의 내용들을 정의하는 곳. 
3) Model : 이 함수가 수행될 때, 데이터베이스와 interaction이 필요할 때는 SQL를 수행해야 하지만 django model를 활용하면 python 코드로 데이터베이스와 통신할 수 있음.
4) Template 엔진 : 웹 브라우저에게 HTML 문자열을 응답을 주려고 할 때에 그 때 이 Template 엔진을 활용하게 되면 훨씬 수월하게 조합할 수 있다. 즉, 복잡한 문자열을 손쉽게 조합하기 위한 문자열 렌더링 엔진이다. ex) 네이버 들어가서 우클릭 -> 페이지 소스 보기를 클릭하면 HTML 문자열이 나온다. 
- 이러한 기본 구조를 django는 app 하나에서 다 지원하고 있다.
