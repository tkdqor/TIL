## 프론트엔드와 협업을 위한 Swagger
- 프론트엔드 및 백엔드 개발자가 서로 협업하기 위해서 중간에 기준이 되어주는 Swagger라는 API 명세서를 만드는 데 도움을 주는 것을 사용해보자.
- 공식문서 https://www.django-rest-framework.org/topics/documenting-your-api/ 에서는 Documenting your API라고 해서 API 문서를 작성하는 것을 지원해주고 있다.
  - 해당 문서 내려보면 Third party packages 부분에 보면, drf-yasg라는 것을 사용해서 우리 코드를 연동할 수 있다. 
  - https://github.com/axnsan12/drf-yasg/ 여기를 보면 drf-yasg의 소스코드가 나와있다. 
  - 그리고 https://drf-yasg.readthedocs.io/en/stable/ 이렇게 공식문서가 따로 존재한다.


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
```

- 영


