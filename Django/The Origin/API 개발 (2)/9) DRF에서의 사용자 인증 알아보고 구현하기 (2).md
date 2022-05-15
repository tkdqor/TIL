## DRF에서의 사용자 인증 알아보고 구현하기 (2)
- 이번에는 토큰 인증을 구현해보자. 그래서 일단 다음 명령어로 accounts 앱을 만들어준다.

```python
django-admin startapp accounts
```

- 그리고 accounts 앱 내부의 views.py로 가서, 인증 및 권한을 체크해주는 함수를 만들자.

```python
from django.contrib.auth import authenticate
from rest_framework.authtoken.models import Token
from rest_framework.response import Response
from rest_framework.decorators import api_view
...

@api_view(['POST'])      # View 함수를 API 서버로 사용하기 위해 설정
def login_view(request):
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(request, username=username, password=password,)

    if user:
        token, _ = Token.objects.get_or_create(user=user)
        return Response({'token': token.key})
    else:
        return Response(status=401)

```

- **먼저 login_view라는 함수를 만든다. 그래서 아이디와 비밀번호를 받아주고, authenticate 함수를 사용해서 user 변수를 정의한다.**
- 만약 로그인이 되었을 때, 즉 user가 있을 때 --> 토큰을 생성해서 토큰 처리를 해줘야 한다. 그러기 위해 from rest_framework.authtoken.models import Token 이렇게 import를 해준다.
  - **그리고 get_or_create로 토큰이 없으면 생성해준다.** 
  - login_view 함수의 return 값을 주기 위해 from rest_framework.response import Response 이렇게 입력을 먼저 해준다. 
  - 그런다음, return Response({'token': token.key}) 이렇게 token의 key를 return 값으로 준다.

- 로그인을 못했을 때는, 401 에러를 주기 위해 return Response(status=401) 코드 입력.

- **그리고 해당 함수 View는 API 서버로 사용하는 것이기 때문에, from rest_framework.decorators import api_view 이걸 가져와준다.**
  - **그리고 해당 함수를 어떤 method로 사용할지 이 @api_view 데코레이터 괄호 안에다가 적어줘야 한다.**


* * *
- **이제 이걸 urls.py에 가서 연결을 해주자.**

```python
from accounts.views import login_view
...

urlpatterns = [
    path('login/', login_view),
    ...
]
```

- **이렇게 accounts 앱의 login_view를 가져와서 연결해준다.**
  - 그전에, settings.py에 

```python
INSTALLED_APPS = [
    ...
    'rest_framework.authtoken'
]
```

- **이렇게 추가해줘야 한다. 그리고 바로 migrate를 해줘야 한다. 그래야 token table이 생성된다.**


- **그 다음, postman을 이용해서 http://localhost:8000/login/ 해당 url로 POST 방식으로 Body를 클릭해서 username / password라는 KEY를 만들고 admin 계정의 데이터를 넣어주면**

<img width="866" alt="image" src="https://user-images.githubusercontent.com/95380638/168482897-d0e4928a-593e-47a9-8a72-ce1045b861a3.png">

- **다음과 같이 token이 발생된 것을 확인할 수 있다.**
- 비밀번호를 다르게 하면 401 에러가 발생된다. 

* * *
- **이렇게 로그인 시, 클라이언트쪽에서는 토큰 값을 얻을 수 있다. 그리고 추가로, settings.py에 코드를 추가해보자.**

```python
# API 서버 인증 관련
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.TokenAuthentication',
    ]
}
```

- **이렇게 우리가 사용 할 인증 체계를 설정해줄 수 있다. 여기에 우리가 토큰 인증도 사용할 것이기 때문에 TokenAuthentication도 추가해준다.**
  - 여기에 "권한"과 관련된 코드들도 미리 추가해주기. https://www.django-rest-framework.org/api-guide/permissions/ 해당 공식 문서 참고해서 settings.py에 추가.

```python
# API 서버 인증 관련
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.TokenAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ]
}
```

- **그래서 API 서버와 관련해서 인증이 필요하도록 설정했다. 이제 로그인과 관련된 API를 날리면 바로 에러가 뜬다.**

<img width="873" alt="image" src="https://user-images.githubusercontent.com/95380638/168483442-8cebb085-f569-4b6a-b330-b40058db03b1.png">


- 즉, 지금은 막아놓은 상태이고, token을 넣어야지 응답을 해줄 수 있게 했다. 
  - **그래서 이걸 제대로 요청을 날리려면 --> 요청할 때 토큰을 넣어줘야 한다.**
  - postman에서 http://localhost:8000/login/ 를 POST로 날릴 때, Headers를 클릭한 다음, KEY에 Authorization을 입력하고 VALUE에는 'Token 토큰 값' 이렇게 넣어주면 된다.
  - 91806eeaf99a623177ae000dda3d41ebeed6cc2d 이렇게 미리 복사해두어야 한다. 그래서 Token 91806eeaf99a623177ae000dda3d41ebeed6cc2d 이렇게 된다.

<img width="853" alt="image" src="https://user-images.githubusercontent.com/95380638/168484009-c17ea800-5046-4bbf-9304-ebd3a3b76ac0.png">

- 이러면 이제 정상적으로 로그인 처리가 된다.
- **지금은 우리가 토큰으로 우리가 누군지 인증을 하게 된 것이다.**

* * *
- 위의 과정 까지는, 로그인 페이지에 접속할 수 있게끔 토큰으로 인증을 해본 것이다.
- 지금부터는, 여기에 추가로 로그인 페이지는 누구나 들어올 수 있게 해줘야 하기 때문에 코드를 추가해보자. https://www.django-rest-framework.org/api-guide/authentication/ 를 참고해서, accounts앱의 views.py에서 @permission_classes([IsAuthenticated]) 해당 코드를 함수 위쪽에 추가해준다.

```python
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import AllowAny
...

@api_view(['POST'])      # View 함수를 API 서버로 사용하기 위해 설정
@permission_classes([AllowAny])   # 누구나 접근할 수 있게 권한 설정
def login_view(request):
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(request, username=username, password=password,)

    if user:
        token, _ = Token.objects.get_or_create(user=user)
        return Response({'token': token.key})
    else:
        return Response(status=401)
```

- **그러면 Headers에 토큰을 추가하지 않고 API를 요청해도 로그인 페이지에 접속할 수 있게 된다.**

- **이걸 설정해주지 않은 다른 API URL은 그냥 요청하면 접근이 되지 않는다. 그래서 이제부터는 Headers에 토큰을 무조건 입력해야 API 요청이 된다.**
  - ex) http://localhost:8000/posts/ 를 GET으로 요청할 때도 headers에 -> KEY에 Authorization을 입력하고 VALUE에는 'Token 토큰 값'을 입력해주면 

<img width="861" alt="image" src="https://user-images.githubusercontent.com/95380638/168484635-d9b59f4a-5366-460f-b47a-89e1da79d7e3.png">

- 이렇게 응답을 잘 받을 수 있다.







