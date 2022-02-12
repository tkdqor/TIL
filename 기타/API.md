## API
- Application Programming Interface의 약자로, front-end가 back-end에 요청을 할 때 특정 규칙에 맞게 요청해야 하는데 이러한 사용 규칙을 제공하는 것을 API라고 한다.


### open API
- 누군가가 back-end를 만들어놓고 여기에 주소와 사용 규칙을 공개한 것을 의미.
- 그래서 우리는 이 back-end의 사용규칙과 주소만 알면, front-end만 만들어도 얼마든지 요청하고 데이터를 가져와서 사용할 수 있다.
- 우리는 규칙에 맞게 전송하기만 하면, 지도 / 결제 / 채팅 등 다양한 기능들을 사용할 수 있다.

ex) 우리가 카카오 책 검색 open API 서버 주소로 어떤 책에 대한 정보를 요청하면 -> API 서버에서는 자체 DB를 뒤져서 우리가 원하는 정보를 다시 전달해준다.
- 이러한 요청과 응답은 정해진 API 형식에 맞춰서 전달해야 한다. 이러한 형식이 작성된 문서를 API 가이드라고 한다.
- 요즘에는 거의 모든 API에서 JSON형태로 정보가 전달된다.

* console.log() 함수 : 개발자 도구의 콘솔 창에 표시하는 함수


### open API를 통해 받은 JSON
- open API를 통해 받은 JSON 데이터를 실제 HTML 파일에 출력하려면 -> JSON 데이터를 JavaScript로 접근해야 한다.
- 해당 JSON 데이터를 JavaScript로 console에 표시할 수 있다.
  - 그리고나서, Jquery를 이용해 -> html의 특정 element에 어떤 요소를 넣어줄 수 있다. 
  - <script></script> 이렇게 JavaScript 코드 사이에 HTML 특정 element를 설정하고 해당 데이터를 보여줄 수 있다. 
