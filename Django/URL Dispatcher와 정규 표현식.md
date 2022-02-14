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


### URL patterns 예시
```python
from django.urls	import path,	re_path
from shop	import views

urlpatterns	=	[
  path('',	views.item_list,	name='item_list'),																#	Item 목록
  path('new/',	views.item_new,	name='item_new'),																#	새 Item
  
  path('<int:id>/',	views.item_detail,	name='item_detail'),										#	Item 보기
  re_path(r'^(?P<id>\d+)/$',	views.item_detail,	name='item_detail'),					#	혹은 re_path	활용
  
  path('<int:id>/edit/',	views.item_edit,	name='item_edit'),									#	Item 수정
  path('<int:id>/delete/',	views.item_delete,	name='item_delete'),						#	Item 삭제
  
  path('<int:id>/reviews/',	views.review_list,	name='review_list'),						#	리뷰 목록
  path('<int:item_id>/reviews/<int:id>/edit/$',	views.review_edit,							#	리뷰 수정
        name='review_edit'),
  path('<int:item_id>/reviews/<int:id>/delete/$',	views.review_delete,			    #	리뷰 삭제
        name='review_delete'),
]

```

- path 함수를 통해서 우리가 원하는 형태를 구성할 수 있는데, re_path 함수는 정규표현식 모듈의 이름이 붙여져 있다. re는 regular expression으로 말그대로 정규표현식을 뜻한다. 그래서 re_path 함수는 
  정규표현식 패턴을 쓸 수 있는 path 함수이다. 그냥 path 함수는 정규표현식이 내부적으로 적용이 되어 있다. 
  - path('<int:id>/',.... -> 여기에서 int는 정수가 1회 이상 반복되는 정수를 의미. 따라서 1이라고 입력해도 매칭되고, 12345라고 해도 매칭이 된다. 정규표현식에서는 \d+ 이렇게 사용된다.


### path() 와 re_path() 
- django 2점대부터 2가지로 분리.
- django.urls.re_path()와 django.conf.urls.url()과 사용방법이 동일.

- django.urls.path()는, 기본 지원되는 path converters를 통해 정규표현식 기입이 간소화된다. 자주 사용하는 패턴이 있다면 Converter로 등록해서 재사용이 가능하다.

```python
path('articles/<int:year>/', views.year_archive),

re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
```

- re_path() 의 경우는, 먼저 소괄호안에 ?P를 써주고 [0-9]{4} 이렇게 패턴을 써준다.(ex. 정수가 연속으로 4회 반복됨 / 꼭 4자리를 입력해야 된다는 것.) 요청된 URL이 이 패턴에 부합될 경우, 정해준 year라는 변수에 패턴에 부합되는 부분을 문자열로 뽑아서 넣어주고, View 함수가 호출될 때 인자로 넘겨준다.


### 기본제공되는 Path Converters
- IntConverter -> r"[0-9]+"  
- StringConverter -> r"[^/]+"
- UUIDConverter -> r"[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}"
- SlugConverter (StringConverter 상속) -> r"[-a-zA-Z0-9_]+"
- PathConverter (StringConverter 상속) -> r".+"
