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
...
def login_view(request):
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(request, username=username, password=password)

    if user.is_authenticated:
        token, _ = Token.objects.get_or_create(user=user)
        return Response({'token': token.key})
    else:
        pass


```

- **먼저 login_view라는 함수를 만든다. 그래서 아이디와 비밀번호를 받아주고, authenticate 함수를 사용해서 user 변수를 정의한다.**
- 만약 로그인이 되었을 때, 즉 user가 있을 때 --> 토큰을 생성해서 토큰 처리를 해줘야 한다. 그러기 위해 from rest_framework.authtoken.models import Token 이렇게 import를 해준다.
  - **그리고 get_or_create로 토큰이 없으면 생성해준다.** 
  - login_view 함수의 return 값을 주기 위해 from rest_framework.response import Response 이렇게 입력을 먼저 해준다. 
  - 그런다음, return Response({'token': token.key}) 이렇게 token의 key를 return 값으로 준다.
