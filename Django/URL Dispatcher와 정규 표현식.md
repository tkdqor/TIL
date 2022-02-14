## URL Dispatcher
- Django에는 URL Dispatcher라는 시스템이 있다. 
  - 웹 브라우저가 client이고 웹페이지에서 어떠한 요청이 오면, 예를 들어 주소를 입력하고 enter를 할 때나 검색어를 입력하고 검색할 때나 댓글을 달 때 등등 언제나 서버로 요청이 갈 때에는 기본적으로 주소 base에서 
    서버로 전달이 된다.
  - 해당 주소의 앞부분이 실제 server의 위치, host 주소, 도메인을 의미하고 / 뒷부분인 request URL로 -> server에서 어떠한 로직에서 처리할 것인지 server가 결정하게 된다. 이에 대한 것이 URL Dispatcher 시스템이다.

- 특정 URL pattern이 왔을 때, 어떠한 View에서 처리하겠다라는 일련의 매핑 리스트가 존재하게 된다. 이 리스트가 urlpatterns라는 이름의 리스트가 된다.
  - 그 urlpatterns의 최상위 urlpatterns는 프로젝트 디렉터리 -> settings.py에서 ROOT_URLCONF = 'askcompany.urls' 이렇게 ROOT_URLCONF 라는 설정으로 최상위 urlpatterns를 지정하게 된다.
    - 이 최상위 urlpatterns로부터 include를 통해 TREE 구조로 확장할 수 있다.

```python
from django.conf.urls.static import static
from django.contrib import admin
from django.urls import path, include      # include를 새롭게 추가
from django.conf import settings


urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog1/', include('blog1.urls')),   # 새롭게 url를 만들어주고 해당 url은 blog1 앱의 urls.py로 보내주기
    path('instagram/', include('instagram.urls')),  # 새롭게 url를 만들어주고 해당 url은 instagram 앱의 urls.py로 보내주기
    path('accounts/', include('accounts.urls')),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

- 위의 코드들은 프로젝트 디렉터리 내부의 urls.py 내용이다. 위의 설명에서 ROOT_URLCONF = 'askcompany.urls' 이렇게 설정한 것 처럼 -> URL 요청이 들어왔을 때, 가장 먼저 askcompany라는 프로젝트 내부의 urls.py가 최상위로 확인하게 된다.
  - 또한, 여기서 path 함수를 통해 리스트를 채워나가게 된다. 
  - 그리고 TREE 구조로 확장하기 위해서 include 함수를 사용하여 -> 하위에 있는 urlpatterns를 포함시킬 수 있는 것이다.

- 어떠한 형식으로든 우리는 최상위 ROOT_URLCONF로부터 시작을 해서 차근차근 include를 해나가면, django가 읽어서 하나의 리스트 구조로 메모리상에 유지하고 있다가 -> HTTP 요청이 들어올 때마다, 
  등록된 urlpatterns 상의 매핑 리스트를 처음부터 순차적으로 훝어서 URL 매칭을 시도한다.
  - **매칭이 되는 URL Rule이 다수 존재하더라도, 처음 Rule만을 사용한다.** 그래서 URL이 겹치지 않도록 설계해서 우리가 원하는 View가 호출될 수 있도록 구성을 해야 한다.
  - 매칭되는 URL Rule이 없을 경우, 404 Page Not Found 응답 발생

```python
from django.urls import path, re_path
from . import views


urlpatterns = [
    path('', views.post_list),
    path('<str:pk>/', views.post_detail_2),         
    path('<int:pk>/', views.post_detail),
    # re_path(r'(?P<pk>\d+)/$', views.post_detail),
]

```

- 위에는 instagram 앱 내부의 urls.py이다. 만약, 이 상태에서 URL이 '/instagram/10/' 이라는 요청이 왔다면 -> 정규표현식으로 작성한 코드인 <str:pk> 그리고 <int:pk> 이렇게 2군데에서 매칭이 된다.
  - str은 문자열을 의미하고, int는 숫자형식의 문자열을 의미.
  - 지금의 케이스에서는, 항상 앞의 pattern에 매칭이 되기 때문에 항상 post_detail_2라는 View 함수가 호출이 되고 두번째에 있는 post_detail이라는 View 함수는 절대 매칭이 되지 못하고 호출이 되지 못한다.
