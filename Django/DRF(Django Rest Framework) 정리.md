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

