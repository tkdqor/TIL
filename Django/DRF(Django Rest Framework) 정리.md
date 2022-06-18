## DRF란
- **Django REST Framework는 Django를 기반으로 REST API 서버를 만들기 위한 라이브러리이다.**
  - DRF는 어디까지나 Django를 기반으로 한 라이브러리이기 때문에 전반적으로 Django의 개발 흐름을 따라가게 된다.

- API는 웹뿐만 아니라 앱과 같은 다양한 플랫폼의 백엔드 서비스를 위해 JSON과 같은 규격화된 데이터를 제공한다. 따라서 DRF를 사용하면 기존에는 자체적인 웹 템플릿에게 바로 데이터를 전달해주는 Django 프로젝트에 국한되었던 것을 -> JSON과 같은 양식으로 다양한 플랫폼의 클라이언트에게 데이터를 제공해줄 수 있는 API 서버를 만들 수 있게 된다.

<br>

### @api_view()
```python
@api_view(['GET'])
def HelloAPI(request):
    return Response("hello world!")
```

- **위와 같이 @api_view(['GET'])라는 데코레이터는, helloAPI라는 함수가 API로 동작하기 위해 앞뒤로 함수를 감싸 실제 API로 동작할 수 있도록 해준다.** 따라서 HelloAPI라는 함수가 GET 요청을 받을 수 있는 API라는 것을 표시해준다고 보면 된다.

- **여기서 request 객체는 사용자가 우리 DRF API 서버로 요청을 보낼 때, 요청이 GET, POST 등 어떤 타입인지, 사용자가 요청을 보낼 때 무슨 데이터를 함께 보내주었는지 등 정보를 담고 있다.**
  - ex) request.method / request.data로 접근 가능

- **그리고 결과를 반환할 때는 Response라는 클래스를 사용한다.**
  - request와 마찬가지로 Response에는 응답에 대한 정보가 담겨져 있다. response.data에는 응답에 포함되는 데이터 / response.status에는 응답에 대한 상태가 나타난다.

<br>

### 상태 코드
- 상태 코드는 요청에 대한 응답이 어떤 상태인지 나타내주는 규격화된 코드값이다. HTTP에서 정의하고 있는 값으로, HTTP를 사용하는 모든 웹 개발자들이 통용적으로 사용하고 있다.
- 따라서, DRF의 Response에는 이 상태 코드가 꼭 포함되어 있다. 몇 가지 대표적인 상태 코드들은 아래와 같다.

```python
HTTP_200_OK
# 데이터를 요청하는 GET 요청이 정상적으로 이뤄졌을 때 응답에 나타나는 상태값

HTTP_201_CREATED
# 데이터를 생성하는 POST 요청이 정상적으로 이뤄졌을 때 응답에 나타나는 상태값

HTTP_206_PARTIAL_CONTENT
# 데이터를 일부 수정하는 PATCH 요청이 정상적으로 이뤄졌을 때 응답에 나타나는 상태값

HTTP_400_BAD_REQUEST
# 잘못된 요청을 보냈을 때(클라이언트가) 응답에 나타나는 상태값

HTTP_401_UNAUTHORIZED
# 인증이 필요한데 인증 관련 내용이 요청에 없을 때 응답에 나타나는 상태값

HTTP_403_FORBIDDEN
# 클라이언트가 접근하지 못하도록 막아놓은 곳에 요청이 왔을 때 응답에 나타나는 상태값

HTTP_404_NOT_FOUND
# 클라이언트가 요청을 보낸 곳이 잘못된 URL일 때(즉, 리소스가 없을 때) 응답에 나타나는 상태값

HTTP_500_INTERNAL_SERVER_ERROR
# 서버 쪽에서 코드가 잘못되었을 때 응답에 나타나는 상태값
```

<br>

### HTTP의 헤더
<img width="189" alt="image" src="https://user-images.githubusercontent.com/95380638/174439996-b0df3f13-a391-4d62-be5c-303222034988.png">

- **위와 같이 Hello Api 화면에 보여지는 데이터들이 바로 HTTP의 헤더 데이터이다.** 
- HTTP는 웹의 프론트엔드와 백엔드 간 요청/응답을 위한 프로토콜이다. HTTP가 요청/응답을 할 때 누구나 같은 방식으로 통신을 하기 때문에 통일된 규격이 필요하고, 그 규격 내에 있는 주요 정보를 담아 놓은 것이 바로 HTTP의 헤더이다.
  - 요청/응답에 대한 결과 상태 및 데이터의 타입, 상태 코드 등이 전부 HTTP 헤더에 포함된다.

<br>

### Django VS DRF 차이점
- **Pure Django**
  - 개발 목적 : 웹 풀스택 개발
  - 개발 결과 : 웹 페이지를 포함한 웹 서비스
  - 응답 형태 : HTML
  - 다른 파일 : templates

- **Django REST Framework**
  - 개발 목적 : 백엔드 API 서버 개발
  - 개발 결과 : 여러 클라이언트에서 사용할 수 있는 API 서버
  - 응답 형태 : JSON
  - 다른 파일 : serializers.py

<br>

### DRF Serializer
- **Serializer의 사전적 의미는 직렬화이다. 직렬화는 Django 프로젝트에서 내가 만든 모델로부터 뽑은 queryset, 즉 모델 인스턴스를 JSON 타입으로 바꾸는 것이다.**
- DRF 내에서 데이터가 저장될 때는 Django의 모델을 통해 저장된다. 따라서 Django에서의 데이터는 JSON과 같은 포맷이 아닌, python 객체의 형태로 저장이 된다.
- 우리가 만들 API 서버는 이러한 데이터들을 클라이언트에게 보내주는 역할을 해야한다.
  - 이 때, 어떤 작업도 하지 않고 데이터를 그대로 보내준다면 클라이언트는 읽지도 못하는 python 데이터를 받게될 것이다.
  - **따라서 이를 위해 python 데이터를 읽을 수 있는 문자열(JSON 등)로 변환하여 보내주어야 하고, 이렇게 python 데이터 객체를 문자열로 변환하는 작업을 직렬화, Serializer라고 이해하면 된다.**

- **반대로 클라이언트가 데이터를 DRF 서버에 보내주는 경우도 있다.**
  - ex) POST 요청을 보내는 API를 클라이언트가 사용해 데이터를 생성하는 경우, 클라이언트는 API 요청에 데이터를 JSON 등 문자열 형태로 입력하여 보내줄 것이다.
- DRF API 서버 입장에서는 이 문자열을 그대로 모델을 통해 저장할 수 없다. 모델을 통해 저장하려면 데이터가 python 객체의 형태여야 하기 때문이다. **따라서 앞선 경우와 반대로 JSON 등 문자열을 python 데이터 객체로 변환하는 작업이 필요한데, 이것을 역직렬화, Deserializer라고 한다.**

- 우리가 앞으로 만들 Serializer는 직렬화와 역직렬화 기능을 동시에 갖고 있다. 즉, 클라이언트와 서버 API 간 데이터 양식을 맞춰주는 변환기라고 생각하면 된다.
  - [관련해서 좋은 글](https://velog.io/@jewon119/TIL136.-DRF-Serializer%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC)




