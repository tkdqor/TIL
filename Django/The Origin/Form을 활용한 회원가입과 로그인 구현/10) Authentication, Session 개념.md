## Authentication, Session 개념
- **루트 디렉터리의 settings.py를 보면, 여러가지 변수들이 있는데 그 중에서도 MIDDLEWARE와 AUTH_PASSWORD_VALIDATORS 이런 부분들을 공부해보자.**

### MIDDLEWARE
- **미들웨어라는 건, 우리가 url pattern을 정의하고 View에 연결시켜서 View에서 요청을 받고 응답을 하는 과정에서 -> View가 요청을 받기 전과 View가 응답을 한 다음에 호출이 되는 것들을 보고 
  미들웨어라고 부른다.**

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]
```

- **다음은 settings.py에 정의된 MIDDLEWARE이다. 여기서 SessionMiddleware과 AuthenticationMiddleware 이 2개에 대해 알아보자.**


- **우리의 가상환경 디렉터리안에 모든 코드들을 다 볼 수 있다. 그래서 가상환경 디렉터리 - lib - ... django - contrib - auth - middleware.py로 가서 클릭해보자.**

```python
from django.contrib import auth
from django.contrib.auth import load_backend
from django.contrib.auth.backends import RemoteUserBackend
from django.core.exceptions import ImproperlyConfigured
from django.utils.deprecation import MiddlewareMixin
from django.utils.functional import SimpleLazyObject


def get_user(request):
    if not hasattr(request, "_cached_user"):
        request._cached_user = auth.get_user(request)
    return request._cached_user


class AuthenticationMiddleware(MiddlewareMixin):
    def process_request(self, request):
        if not hasattr(request, "session"):
            raise ImproperlyConfigured(
                "The Django authentication middleware requires session "
                "middleware to be installed. Edit your MIDDLEWARE setting to "
                "insert "
                "'django.contrib.sessions.middleware.SessionMiddleware' before "
                "'django.contrib.auth.middleware.AuthenticationMiddleware'."
            )
        request.user = SimpleLazyObject(lambda: get_user(request))

```

- **해당 코드에서 AuthenticationMiddleware 클래스가 바로 우리가 settings.py에서 봤던 것과 동일하다.**
- **settings.py를 보면 'django.contrib.auth.middleware.AuthenticationMiddleware', 이렇게 나와있는데 여기서 Auth... 이렇게 대문자로 되어있기 때문에
  클래스를 가져왔다는 것을 알 수 있다.**
  
- 그래서 이 클래스를 가져왔을 때 실행이 되는 코드를 보면, request를 받을 때 session이 있는지 확인을 하고 처리를 한 다음에 get_user 함수를 실행한다. get_user 함수는 request에 있는 
  session 값을 가지고, 데이터베이스에 있는 session Key를 가지고 데이터를 비교해서 그 유저가 누군지를 가져오는 그 행위를 일컫는 코드를 의미한다. 그래서 request.user = SimpleLazyObject(lambda: get_user(request)) 
  이렇게 request.user에 해당 내용을 넣어준다. **그렇기 때문에 우리가 항상 request.user를 가지고 데이터를 사용할 수 있게 되는 것이다.**
  - 즉, settings.py의 'django.contrib.auth.middleware.AuthenticationMiddleware', 이 부분이 있기 때문에 우리가 request.user를 사용할 수 있게 되는 것이다.
  
  
- django의 내장된 사용자 모델인 User의 경우, 로그인을 했을 때 우리가 인증에 대한 form를 가져다 쓰면서 authenticate라는 사용자 인증 함수를 form 안에서 실행되는 걸 확인할 수 있었다.
  - 그리고 django에서 권한은, 특정 모델의 CRUD를 기준으로 해서 권한을 나타내게 되어있다.
  
  
  
