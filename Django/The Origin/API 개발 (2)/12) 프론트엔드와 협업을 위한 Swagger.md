## 프론트엔드와 협업을 위한 Swagger
- 프론트엔드 및 백엔드 개발자가 서로 협업하기 위해서 중간에 기준이 되어주는 Swagger라는 API 명세서를 만드는 데 도움을 주는 것을 사용해보자.
- 공식문서 https://www.django-rest-framework.org/topics/documenting-your-api/ 에서는 Documenting your API라고 해서 API 문서를 작성하는 것을 지원해주고 있다.
  - 해당 문서 내려보면 Third party packages 부분에 보면, drf-yasg라는 것을 사용해서 우리 코드를 연동할 수 있다. 
  - https://github.com/axnsan12/drf-yasg/ 여기를 보면 drf-yasg의 소스코드가 나와있다. 
  - 그리고 https://drf-yasg.readthedocs.io/en/stable/ 이렇게 공식문서가 따로 존재한다.

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

- **이렇게 swagger 화면이 보이게 된다.** 
  - 해당 화면을 보면 우리가 만들었던 calculator, login, posts 관련 API가 다 들어있다.
  - **그래서 이렇게 우리 코드를 연동해서 swagger를 이용해서 API 명세서(API 문서)를 만들 수 있는 것이다.** 이걸 프론트엔드 개발자한테 제공해주면 되게 좋아한다. 
  - 우리가 이걸 만들기 위해 별도로 타이핑을 치지 않았기 때문에, 패키지 하나만 연동시켰는데 이렇게 간단하게 만들 수 있는 것이다.

- 프론트엔드 개발자들은 이걸 보고 '아, posts 라는 것에 리스트를 가져올려면 이렇게 해야 되는구나, 그리고 이러한 데이터,필드값을 주는구나' 라고 알 수 있다. 


