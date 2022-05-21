## 프론트엔드와 협업을 위한 Swagger
- 프론트엔드 및 백엔드 개발자가 서로 협업하기 위해서 중간에 기준이 되어주는 Swagger라는 API 명세서를 만드는 데 도움을 주는 것을 사용해보자.
- 공식문서 https://www.django-rest-framework.org/topics/documenting-your-api/ 에서는 Documenting your API라고 해서 API 문서를 작성하는 것을 지원해주고 있다.
  - 해당 문서 내려보면 Third party packages 부분에 보면, drf-yasg라는 것을 사용해서 우리 코드를 연동할 수 있다. 
  - https://github.com/axnsan12/drf-yasg/ 여기를 보면 drf-yasg의 소스코드가 나와있다. 
  - 그리고 https://drf-yasg.readthedocs.io/en/stable/ 이렇게 공식문서가 따로 존재한다.

- **swagger는 프로젝트 내 많은 API를 한꺼번에 문서화하여 볼 수 있게 하는 패키지이다.**
- **drf-yasg란, Swagger를 사용하기 위해 설치하는 패키지로 django로 정의된 API를 문서화할 수 있는 패키지를 의미한다.**

* * *

- 해당 공식문서 Usage https://drf-yasg.readthedocs.io/en/stable/readme.html#usage 부분에 들어가면 사용방법이 나와있다. 

```terminal
pip install -U drf-yasg
```
- 이렇게 먼저 설치. 그리고 settings.py에 install app에 추가.

```python
INSTALLED_APPS = [
    ...
    'drf_yasg',
]
```

- 그 다음에는 urls.py에 코드 추가.

```python
from django.urls import path, include, re_path
# drf-yasg 설정
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi


# drf-yasg 설정 코드
schema_view = get_schema_view(
    openapi.Info(
        title="Snippets API",
        default_version='v1',
        description="Test description",
        terms_of_service="https://www.google.com/policies/terms/",
        contact=openapi.Contact(email="contact@snippets.local"),
        license=openapi.License(name="BSD License"),
    ),
    public=True,
    permission_classes=[permissions.AllowAny],
)

urlpatterns = [
    # drf-yasg 설정
    path(r'^swagger(?P<format>\.json|\.yaml)$', schema_view.without_ui(cache_timeout=0), name='schema-json'),
    path(r'^swagger/$', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
    path(r'^redoc/$', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
    ...
]
```

- 여기까지 설정하면 연결 자체는 끝이 났다. 그리고나서 http://localhost:8000/swagger/ 라고 브라우저에 입력해보면
<img width="728" alt="image" src="https://user-images.githubusercontent.com/95380638/169634622-ded315c1-a773-4780-afc4-6beac6896f13.png">
<img width="1343" alt="image" src="https://user-images.githubusercontent.com/95380638/169634824-824daed0-8680-4974-9cc2-d2e9f10e841f.png">


- **이렇게 swagger 화면이 보이게 된다.** 
  - 해당 화면을 보면 우리가 만들었던 calculator, login, posts 관련 API가 다 들어있다.
  - **그래서 이렇게 우리 코드를 연동해서 swagger를 이용해서 API 명세서(API 문서)를 만들 수 있는 것이다.** 이걸 프론트엔드 개발자한테 제공해주면 되게 좋아한다. 
  - 우리가 이걸 만들기 위해 별도로 타이핑을 치지 않았기 때문에, 패키지 하나만 연동시켰는데 이렇게 간단하게 만들 수 있는 것이다.

- 프론트엔드 개발자들은 이걸 보고 '아, posts 라는 것에 리스트를 가져올려면 이렇게 해야 되는구나, 그리고 이러한 데이터,필드값을 주는구나' 라고 알 수 있다. 
  - **일일이 API 문서를 작성하거나 Postman에 입력하는 것 보다는, 이렇게 python 코드를 연동해서 swagger를 사용하는 것이 훨씬 생산성이 높다.**
  - 여기서도 POST API 하나를 클릭해서 데이터를 넣고 API를 보내볼 수도 있다. 


### swagger에서 API 보내기
- 다만 설정을 해줘야 실제로 swagger에서도 API를 보내볼 수 있다. 즉, serializer를 잘 써줘야 한다.

- 예시로, accounts 앱 내부 views.py에서 작성한 login_view 코드를 수정해주자.

```python
from drf_yasg.utils import swagger_auto_schema
...

@api_view(['POST'])      # View 함수를 API 서버로 사용하기 위해 설정
@permission_classes([AllowAny])   # 누구나 접근할 수 있게 권한 설정
@swagger_auto_schema()
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

- **이렇게 함수 위에다가 @swagger_auto_schema() 라고 관련 데코레이터를 입력해주기. 이걸 입력하기 위해 from drf_yasg.utils import swagger_auto_schema 이렇게 import.**
  - **@swagger_auto_schema() 라는 어노테이션을 command로 들어가보면 되게 다양한 것들이 내포되어있다.** 


- **또한, response해서 값을 줄 수도 있다.**
  - **일단, accounts 앱의 serializers.py를 새롭게 생성해준다.** 

```python
from rest_framework import serializers
from rest_framework.authtoken.models import Token

class LoginSerializers(serializers.Serializer):
    pass

class TokenSerializers(serializers.ModelSerializer):
    class Meta:
        model = Token
        fields = '__all__'
```

- 그리고 다음과 같이 2개의 클래스를 만들어준다. 그리고나서 accounts 앱의 view에다가

```python
from .serializers import TokenSerializers
...

@api_view(['POST'])      # View 함수를 API 서버로 사용하기 위해 설정
@permission_classes([AllowAny])   # 누구나 접근할 수 있게 권한 설정
@swagger_auto_schema(responses={200: TokenSerializers})
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

- **우리가 위에서 설정한 @swagger_auto_schema에 responses로 상태값을 넣어줘야 한다. @swagger_auto_schema(responses={200: TokenSerializers}) 이렇게 우리가 생성한 TokenSerializers를 넣어준다.**

- 그리고 다시 serializers.py로 가서 LoginSerializers 클래스를 수정하자.

```python
from rest_framework import serializers
from rest_framework.authtoken.models import Token

class LoginSerializers(serializers.Serializer):
    username = serializers.CharField()
    password = serializers.CharField()

class TokenSerializers(serializers.ModelSerializer):
    class Meta:
        model = Token
        fields = '__all__'
```

- username과 password를 추가해준다. 그 다음, views.py로 다시 가서

```python
from .serializers import TokenSerializers, LoginSerializers
...

@api_view(['POST'])      # View 함수를 API 서버로 사용하기 위해 설정
@permission_classes([AllowAny])   # 누구나 접근할 수 있게 권한 설정
@swagger_auto_schema(request_body=LoginSerializers, responses={200: TokenSerializers})
def login_view(request):
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(request, username=username, password=password,)

    if user:
        token, _ = Token.objects.get_or_create(user=user)
        return Response({'token': token.key})
    else:
        return Response(status=401)
        

# 클래스 기반 뷰 하나 생성
class LoginView(APIView):
    permission_classes = [AllowAny]

    @swagger_auto_schema(request_body=LoginSerializers, responses={200: TokenSerializers})
    def post(self, request):
        username = request.data['username']
        password = request.data['password']
        user = authenticate(request, username=username, password=password,)

        if user:
            token, _ = Token.objects.get_or_create(user=user)
            return Response({'token': token.key})
        else:
            return Response(status=401)
```

- **@swagger_auto_schema(request_body=LoginSerializers, responses={200: TokenSerializers}) 이렇게 request_body를 넣어준다.**
  - 근데, 연결이 안 되고 있다.
  - **그래서 그 밑에 LoginView라는 이름의 클래스 기반 뷰를 이번에는 생성해보기.**
  - username = request.data['username'] 이렇게 request.data라고 해야 swagger에서 login POST에서 생성이 가능하다.


- 그 다음으로 urls.py로 가서

```python
from accounts.views import login_view, LoginView
...

urlpatterns = [
    # path('login/', login_view),
    path('login/', LoginView.as_view()),
    ...
]
```

- **이렇게 path('login/', LoginView.as_view()), LoginView를 연결해주기.**
  - **클래스 기반 뷰로 바꿨더니 이제 swagger 페이지에서 login POST를 클릭했을 때**

<img width="1344" alt="image" src="https://user-images.githubusercontent.com/95380638/169636101-a03d21f0-ce77-4948-9933-f1da0f93c2d2.png">

- **이렇게 연동이 되는 것을 확인할 수 있다.**
  - **그래서 login할 때 어떤 파라미터로 던져야 하는지, username/password 이런 걸 던져줘야 한다고 설명해줄 수 있다. 그리고 200이라는 응답이 떨어졌을 때는 Token를 어떻게 준다는 것을 설명해줄 수 있다.**
  - 해당 Token 부분을 수정하고 싶으면, serializers.py에서

```python
...
class TokenSerializers(serializers.ModelSerializer):
    class Meta:
        model = Token
        fields = ['key']
 ```
 
 - **TokenSerializers의 필드를 fields = ['key'] 이렇게 바꿔줄 수 있다.**
 - 그리고 "Try it out" 이라는 버튼을 눌러보면 login POST로 생성도 가능하다.


- **프론트엔드 개발자와 협력할 때, 필드 속성 하나만 바뀌어도 바로 알려줘야 소통 문제가 발생하지 않는다.**
  - 그리고 DRF와 같은 API 서버를 구축할 때는 => API 명세서를 필수적으로 만들어줘야 한다. 나 혼자 사용하려고 만든 것이 아니기 때문이다.





