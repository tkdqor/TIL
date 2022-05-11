## 경로를 다루는 Routers


### DRF 실행 흐름(바탕화면 캡처 참고)
- DRF도 똑같이 클라이언트와 / 서버가 존재
  - 클라이언트는 웹 브라우저, 안드로이드, iOS 등을 의미 

- 클라이언트가 서버에 API 요청을 날리면 -> urls.py에서 url를 매핑해주는데 여기서 Routers라는 개념이 나온다. Routers는 url 매핑을 View를 기반으로 해서 자동으로 작성해주는 클래스이다. 우리가 View만 작성하면, urls.py를 건드리지 않아도 자동으로 작성을 해준다. 
- 그래서 urls.py에서 매핑된 url로 가서 -> View로 전달해준다. View는 모델에서 데이터를 조회하고 다시 클라이언트로 응답을 해주는데, 이 DRF 실행 흐름에서는 template이 없다. 
  - **API는 JSON이나 XML처럼 뭔가 텍스트 기반의 응답을 해준다. 즉, API는 HTML을 응답해주는 목적이 아니기 때문에 template을 사용하지 않는다.**
  - View는 @api_view(), GenericAPIView, ViewSet 등이 DRF에서 제공해주는 함수 클래스이다. 

- 이번 강의부터는 https://www.django-rest-framework.org/ 해당 공식문서를 많이 볼 예정이다. 공식문서는 만든 개발자들이 올려놓은 것이기 때문에 따라하면 잘 되게 되어있다. 

