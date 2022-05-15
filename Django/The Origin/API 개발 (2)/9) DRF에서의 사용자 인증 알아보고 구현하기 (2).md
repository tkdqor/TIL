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
    user = authenticate(request, username=username, password=password)

    if user.is_authenticated:
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
- 이제 이걸 urls.py에 가서 연결을 해주자.


6:25
