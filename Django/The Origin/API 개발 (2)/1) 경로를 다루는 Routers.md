## 경로를 다루는 Routers


### DRF 실행 흐름(바탕화면 캡처 참고)
- DRF도 똑같이 클라이언트와 / 서버가 존재
  - 클라이언트는 웹 브라우저, 안드로이드, iOS 등을 의미 

- 클라이언트가 서버에 API 요청을 날리면 -> urls.py에서 url를 매핑해주는데 여기서 Routers라는 개념이 나온다. Routers는 url 매핑을 View를 기반으로 해서 자동으로 작성해주는 클래스이다. 우리가 View만 작성하면, urls.py를 건드리지 않아도 자동으로 작성을 해준다. 
- 그래서 urls.py에서 매핑된 url로 가서 -> View로 전달해준다. View는 모델에서 데이터를 조회하고 다시 클라이언트로 응답을 해주는데, 이 DRF 실행 흐름에서는 template이 없다. 
  - **API는 JSON이나 XML처럼 뭔가 텍스트 기반의 응답을 해준다. 즉, API는 HTML을 응답해주는 목적이 아니기 때문에 template을 사용하지 않는다.**
  - View는 @api_view(), GenericAPIView, ViewSet 등이 DRF에서 제공해주는 함수 클래스이다. 

- 이번 강의부터는 https://www.django-rest-framework.org/ 해당 공식문서를 많이 볼 예정이다. 공식문서는 만든 개발자들이 올려놓은 것이기 때문에 따라하면 잘 되게 되어있다. 

- 공식문서 관련, Django는 최근에 4.0 버전으로 올라왔다. https://docs.djangoproject.com/en/4.0/contents/ 해당 페이지에서 맨 밑으로 스크롤을 내려보면 Release notes라고 해서 지금까지 발전된 django의 버전들이 차례대로 나와있다. https://docs.djangoproject.com/en/4.0/releases/
  - 여기서 가장 최근 버전인 Django 4.0 release notes를 클릭해보면, 이전 버전보다 어떤 점이 바뀌었는지 친절하게 작성이 되어있다. 그럼 우린 이걸 보면서 뭔가 새로운, 좋은 기능들이 생겼으면 그 기능을 잘 활용하면 되고, 만약에 어떤 것이 사라질 것이다 하면 빠르게 대응할 수 있어야 한다.
  - ex) django 어드민 페이지를 개발할 때, search_field를 작성해서 설명 글 작성할 때 번거로웠는데, 4.0버전부터 개선이 되었다.

- **그래서 항상 내가 사용하는 언어와 프레임워크의 버전을 체크해서 올라갈 때마다 어떤 사항들이 개선되고 추가되었는지 확인 할 필요가 있다.**


### DRF 공식 문서 - Routers - https://www.django-rest-framework.org/api-guide/routers/
- DRF 공식문서에서 API Guide - Routers를 클릭하자. 
- django에 자동으로 url 라우팅을 View를 기준으로 작성해준다.

```python
from rest_framework import routers

router = routers.SimpleRouter()
router.register(r'users', UserViewSet)
router.register(r'accounts', AccountViewSet)
urlpatterns = router.urls
```

- 이렇게 ViewSet 기준으로 작성을 했다면,

```python
URL pattern: ^users/$ Name: 'user-list'
URL pattern: ^users/{pk}/$ Name: 'user-detail'
URL pattern: ^accounts/$ Name: 'account-list'
URL pattern: ^accounts/{pk}/$ Name: 'account-detail'
```

- **이러한 url 패턴들을 만들어주는 것이다. 기존에는 ListView, DetailView 이렇게 작성해줬어야 했는데 DRF에서는 router만 정해주면 알아서 url pattern이 정의된다.**



- **또한, 우리가 꼭 CRUD 관련해서 작성하지는 않는다. 기존 django url 패턴에서 API를 작성할 때, Router를 같이 사용할 수 있는 개념으로 보자.**

```python
from myapp.permissions import IsAdminOrIsSelf
from rest_framework.decorators import action

class UserViewSet(ModelViewSet):
    ...

    @action(methods=['post'], detail=True, permission_classes=[IsAdminOrIsSelf])
    def set_password(self, request, pk=None):
        ...
```

- 위의 코드는 User의 post에서 set_password 함수를 설정했다. 즉, 유저에 대한 패스워드를 바꾸겠다는 의미로 작성되었다. 그래서 User를 바꾸려면 detail이 있어야 한다. 왜냐면 모든 유저가 아닌 특정 한 유저의 비밀번호를 바꾸는 것이기 때문이다. 

```python
URL pattern: ^users/{pk}/set_password/$
URL name: 'user-set-password'
```

- 그래서 다음과 같은 url pattern이 형성된다. 만약 detail=False라면 url에서 pk가 빠지게 된다. 


### Routers가 제공하는 클래스
- SimpleRouter / DefaultRouter / Custom Routers 이렇게 존재한다. 
- SimpleRouter는 딱 간단하게 CRUD만 해주는 것이다. 우리가 리소스라고 표현했던 부분이 prefix로 설명되어있다. lookup은 pk값으로 설명된다. 

- DefaultRouter는 기본적으로 api-root가 들어간다. 

- **그래서 딱 정말 CRUD만 할 때는 SimpleRouter를 사용하고 / 조금 더 복잡하고 많은 걸 할 때는 DefaultRouter를 사용하면 된다.**

```python
url=r'^{prefix}$'
```

- 이런식으로 $표시가 되어있는 건, 이 뒤로는 받지 않겠다는 의미이다. 

* * *
- 우리 프로젝트의 코드들을 살펴보자. 먼저 urls.py에서는

```python
from django.contrib import admin
from django.urls import path, include
from rest_framework import routers
from posts.views import PostModelViewSet, calculator, CalculatorAPIView

# DRF routers 설정
router = routers.DefaultRouter()
router.register('posts', PostModelViewSet)

urlpatterns = [
    path('', include(router.urls)),
    path('admin/', admin.site.urls),
    # 계산기 기능 / 함수 기반 API View
    # path('calculator/', calculator, name='calculator-fbv'),
    # 계산기 기능 / 클래스 기반 API View
    path('calculator/', CalculatorAPIView.as_view(), name='calculator-cbv'),
]
```

- **이런식으로 DefaultRouter를 사용해서 router라는 것을 정의했고, 거기에 PostModelViewSet를 가지고 router에 등록해준 것이다.**
- DefaultRouter이기 때문에 api-root가 먼저 뜬다. 

- **View에 있는 ModelViewSet를 command로 클릭해보면, 결국에는 View라는 우리가 많이 본 원형의 클래스를 상속받고 있다. 그래서 자동으로 url pattern를 작성해준다.**
- **그래서 Routers를 쓰면 -> View를 기반으로 해서 알아서 url pattern를 만들어준다.**

