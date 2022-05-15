## DRF에서 사용자 인증 알아보고 구현하기 (1)
- 이번에는 DRF 사용자 인증에 대해서 알아보기 
- **Django에서는 기본적으로 Session 인증을 사용했었다. 근데 DRF에서는 인증의 종류가 다양하다.**

- **인증은 "입구 지키미"라고 생각하자. 누가 만약에 우리 API 서버에 요청을 날렸을 때 이걸 들어오게 할지 말지에 대한 판단을 하는 단계라고 보면 된다.**
- **권한은 이미 API 서버에 요청으로 들어왔는데, 요청자에 따라서 세부 내용을 다르게 응답해주는 것이라고 보면 된다.** 

- DRF에서도 request.user를 사용할 수 있다. 그리고 우리가 굳이 settings.py에 추가하지 않아도

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ]
}
``` 

- 다음과 같은 인증 체계가 default로 설정되어있다. 


### APIView
- ModelViewSet에서 상속받은 클래스를 제일 끝까지 따라갔을 때 있었던 클래스이다. 이걸 command로 보면, Django의 View 클래스를 상속받고 있다. 
- 그래서 이 APIView를 상속받아서 클래스를 설정하면  

```python
from rest_framework.authentication import SessionAuthentication, BasicAuthentication
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    authentication_classes = [SessionAuthentication, BasicAuthentication]
    permission_classes = [IsAuthenticated]

    def get(self, request, format=None):
        content = {
            'user': str(request.user),  # `django.contrib.auth.User` instance.
            'auth': str(request.auth),  # None
        }
        return Response(content)
```

- **이런식으로 구현할 수 있다. 여기서 authentication_classes라는 변수에 세션 / 베이직 이렇게 설정되어 있는데, --> 만약 우리 API는 private라서 다 막아놔야 한다면, 지금처럼 django 자체에는 세션이든 베이직이든 토큰이든 인증을 기반으로 설정해줄 수 있다.**
  - 즉, 이렇게 인증을 섞어서 사용할 수 있다는 것이다. 
  - 그리고 permission_classes 라는 변수로 권한도 설정해줄 수 있다. 


### BasicAuthentication
- DRF에서는 인증 종류가 많아서 이걸 언제 사용해야 하는지가 중요하다. https://www.django-rest-framework.org/api-guide/authentication/ 해당 공식문서를 보면, BasicAuthentication은 테스트에서만 적합하다고 나와있다. 


### TokenAuthentication
- 토큰 인증은 거의 모든 클라이언트 종류에 적합하다고 공식 문서에 나와있다. 모바일, 웹 등 다양하게 사용한다. 
- 토큰하면 유명한게 JWT라고 해서 토큰 방식이 있다. 이 때는 settings.py에 추가를 해줘야 한다. 그리고 토큰을 생성해서 키를 발급해줘야 한다. 
  - 그래서 클라이언트가 이 키를 서버로 보내야 한다.

- 그래서 토큰으로 인증이 되면, 결국 request.user를 사용할 수 있게 된다. 만약 인증이 되지 않는다면 HTTP 401 Unauthorized 에러가 발생한다. 
  - 또한 curl는 Mac이나 리눅스 계열에서 요청날릴 수 있는 기능이라고 보면 된다. 

- **요즘에는 대부분 JWT 토큰을 사용한다. JSON Web Token의 약자이다.** 
  - 우리가 API 서버의 인증 시스템을 토큰 인증으로 설정하게 되면 -> 클라이언트가 서버한테 특정 조건에 맞게 토큰을 줘야 한다. 그러면 서버는 그 토큰을 보고 '아 이 사람이 인증된 사용자구나 / 아니구나'를 판단해서 인증을 허용해주게 된다. 


### SessionAuthentication
- 우리가 인스타그램 형식을 django로 만들었을 때, 그 때 Ajax 처리를 하는 게 '좋아요 기능'과 같이 -> 전체 페이지가 다 수정되는 것이 아니라 부분적으로 데이터가 refresh 되어야 하는 것들에 사용된다.
  - **만약, 좋아요 버튼을 눌렀다고 해서 새로고침이 되게 한다면 --> 사용성이 떨어지고 데이터 통신도 많이 낭비가 된다. 그 조그마한 데이터를 수정하려고 페이지 전체를 다시 요청을 하는 것이니까 말이다.** 
  - 이럴 때 Ajax 처리를 해주게 되고 이런 경우게 세션 인증이 적합하다. 그리고 세션 인증은 웹에서 많이 사용된다.

- **지금은 API 서버 관점에서 봤을 때 세션 인증이 ...때 적합하다고 하는 것이지, 실제 웹 개발에서는 정말 많이 사용된다.**
  - **사용자가 로그인 했을 때, 그 사용자가 로그인된 게 맞으면 데이터베이스에 세션 값을 만들고, 요청의 응답값으로 다시 HTTP header에다가 세션값을 던져서 쿠키로 클라이언트쪽으로 돌려주게 된다. 그래서 다시 요청을 날릴 때 쿠키의 세션값이 들어가 있다면, 서버가 그 쿠키에 있는 값을 가지고 데이터베이스에서 조회해서 어떤 사용자인지 인증을 허용해주는 시스템이 된다.**


- 이렇게 3가지 종류의 인증을 봤을 때, 요즘에는 JWT 토큰 인증을 많이 사용하고 있다. 
