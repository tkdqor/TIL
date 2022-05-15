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

- 그래서 토큰으로 인증이 되면, 결국 request.user를 사용할 수 있게 된다.
