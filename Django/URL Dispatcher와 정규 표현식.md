## URL Dispatcher
- **Django에는 URL Dispatcher라는 시스템이 있다.** 
  - 웹 브라우저가 client이고 웹페이지에서 어떠한 요청이 오면, 예를 들어 주소를 입력하고 enter를 할 때나 검색어를 입력하고 검색할 때나 댓글을 달 때 등등 언제나 서버로 요청이 갈 때에는 기본적으로 주소 base에서 
    서버로 전달이 된다.
  - **해당 주소의 앞부분이 실제 server의 위치, host 주소, 도메인을 의미하고 / 뒷부분인 request URL로 -> server에서 어떠한 로직에서 처리할 것인지 server가 결정하게 된다. 이에 대한 것이 URL Dispatcher 시스템이다.**

- 특정 URL pattern이 왔을 때, 어떠한 View에서 처리하겠다라는 일련의 매핑 리스트가 존재하게 된다. 이 리스트가 urlpatterns라는 이름의 리스트가 된다.
  - **그 urlpatterns의 최상위 urlpatterns는 프로젝트 디렉터리 -> settings.py에서 ROOT_URLCONF = 'askcompany.urls' 이렇게 ROOT_URLCONF 라는 설정으로 최상위 urlpatterns를 지정하게 된다.**
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

- **위의 코드들은 프로젝트 디렉터리 내부의 urls.py 내용이다. 위의 설명에서 ROOT_URLCONF = 'askcompany.urls' 이렇게 설정한 것 처럼 -> URL 요청이 들어왔을 때, 가장 먼저 askcompany라는 프로젝트 내부의 urls.py가 최상위로 확인하게 된다.**
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

- path 함수를 통해서 우리가 원하는 형태를 구성할 수 있는데, **re_path 함수는 정규표현식 모듈의 이름이 붙여져 있다. re는 regular expression으로 말그대로 정규표현식을 뜻한다. 그래서 re_path 함수는 
  정규표현식 패턴을 쓸 수 있는 path 함수이다.** 그냥 path 함수는 정규표현식이 내부적으로 적용이 되어 있다. 
  - path('<int:id>/',.... -> 여기에서 int는 정수가 1회 이상 반복되는 정수를 의미. 따라서 1이라고 입력해도 매칭되고, 12345라고 해도 매칭이 된다. 정규표현식에서는 \d+ 이렇게 사용된다.


### path() 와 re_path() 
- django 2점대부터 2가지로 분리.
- django.urls.re_path()와 django.conf.urls.url()과 사용방법이 동일.

- django.urls.path()는, 기본 지원되는 path converters를 통해 정규표현식 기입이 간소화된다. 자주 사용하는 패턴이 있다면 Converter로 등록해서 재사용이 가능하다.

```python
path('articles/<int:year>/', views.year_archive),

re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
```

- **re_path() 의 경우는, 먼저 소괄호안에 ?P를 써주고 [0-9]{4} 이렇게 패턴을 써준다.(ex. 정수가 연속으로 4회 반복됨 / 꼭 4자리를 입력해야 된다는 것.) 요청된 URL이 이 패턴에 부합될 경우, 정해준 year라는 변수에 패턴에 부합되는 부분을 문자열로 뽑아서 넣어주고, View 함수가 호출될 때 인자로 넘겨준다.**


### 기본제공되는 Path Converters
- IntConverter -> r"[0-9]+"  
  - +는 1회이상 반복된다는 의미. 그래서 정수가 1회이상 반복된다.
- StringConverter -> r"[^/]+"
  - /를 제외한 어떤 문자열이 1회이상 반복된다는 뜻.
- UUIDConverter -> r"[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}"
  - [0-9a-f] 이 부분이 16진수를 의미. 16진수가 8번 반복, 4번 반복, 이렇게해서 총 36글자가 있어야 매칭이 됨.
- SlugConverter (StringConverter 상속) -> r"[-a-zA-Z0-9_]+"
  - [-a-zA-Z0-9_] 이 부분이 알파벳소문자, 알파벳대문자, 숫자가 하이픈으로 연결된 형태. 마지막엔 언더바.
- PathConverter (StringConverter 상속) -> r".+"
  - 점(.)이 모든 문자열을 의미. 그래서 모든 문자열이 1회이상 반복된다는 의미.

* * *

## 정규표현식
- 거의 모든 프로그래밍 언어에서 지원. 문자열의 패턴, 규칙, Rule을 정의하는 방법이다. 이 정규표현식을 통해서 우리가 원하는 패턴의 문자열을 쉽고 빠르게 찾을 수 있고 변환할 수 있다.
- 문자열 검색이나 치환작업을 간편하게 처리
- **장고 URL Dispatcher에서는 정규표현식을 통한 URL 매칭 문법이 존재한다.**
  - 1글자에 대한 패턴 + 연속된 출연 횟수 지정 
  - 대괄호 내에 1글자에 대한 후보 글자들을 나열
  - ex) [ABC]{2} 이렇게하면, 올 수 있는 글자들이 ABC 이렇게 되고 연속으로 2번 반복한다는 것이다. 이렇게 1글자에 대한 패턴을 정의할 수 있다. 이 패턴을 계속 이어서 나열하는 것이다.


### 다양한 정규표현식 패턴 예시
- 1자리 숫자 -> "[0123456789]" 혹은 "[0-9]" 혹은 r"[\d]" 혹은 r"\d"
  - 0부터 9까지를 \d로 표현가능. 대괄호는 여러 글자들을 나열할 때 한 글자에 대한 여러 후보 문자들을 나열할 때 사용. 그런데, 후보가 1개 밖에 없을 때에는 대괄호를 생략할 수 있다.
- 2자리 숫자 -> "[0123456789][0123456789]" 혹은 "[0-9][0-9]" 혹은 "\d\d"
- 3자리 숫자 -> r"\d\d\d" 혹은 r"\d{3}"
  - 중괄호로 3를 표시하면, 연속해서 3번 나온다는 것을 의미.
- 2자리~4자리 숫자 -> r"\d{2,4}"
  - 2자리 혹은 3자리 혹은 4자리. 중괄호에서 콤마를 사용하면 {2,4}의 경우, 2자리이상 4자리이하가 된다. 
- 휴대폰 번호 -> r"010[1-9]\d{7}"
  - 010 다음에 0이 오는 경우는 거의 없다.. 그래서 [1-9]는 1부터 9까지의 문자열이 1개가 오고, \d는 0부터 9까지를 의미하니까 그게 연속해서 7번 온다는 의미이다.
- 알파벳 소문자 1글자 -> "[abcdefghijklmnopqrstuvwxyz]" 혹은 "[a-z]"
- 알파벳 대문자 1글자 -> "[ABCDEFGHIJKLMNOPQRSTUVWXYZ]" 혹은 "[A-Z]"

- 여기에 추가로 16진수 1글자는 -> [\da-fA-F]+ 이렇게 표현가능.


### 반복횟수 지정 문법
- r"\d" : 별도 횟수 지정 없음 -> 1회 반복
- r"\d{1}" : 1회 반복
- r"\d{2}" : 2회 반복
- r"\d{2,4}" : 2회~4회 반복
- r"\d?" : 0회 혹은 1회 반복
- r"\d*" : 0회 이상 반복
- r"\d+" : 1회 이상 반복

- 정규표현식은 띄워쓰기 하나에도 민감하니까 가독성 이유로 띄워쓰기 하지 말기.

* * *

### 커스텀 Path Converter
```python
from django.urls import path, re_path
from . import views


urlpatterns = [
    path('', views.post_list),
    path('<int:pk>/', views.post_detail),
    path('archives/<int:year>/', views.archives_year),
    
    re_path(r'archives/(?P<year>\d+)/', views.archives_year),
]

```

```
def archives_year(request, year):
    return HttpResponse(f"{year}년 archives")
```

- 이렇게 순서대로 urls.py와 views.py를 설정해주면 매칭이 된다. 만약에 정규표현식을 사용해서 re_path를 사용하면 re_path(r'archives/(?P<year>\d+)/'), 과 같다.
  - **앞에 r이라고 쓴 것은, Raw의 약자로 \(역슬래쉬)를 자동 escape 처리해준다. 원래 r를 빼고 쓴다면**
    - **re_path(r'archives/(?P<year>\\\d+)/'), 이렇게 입력해야 한다. r은 python 기본 문법이다.**
  
- 만약, 모든 정수(지금은 1244353 이렇게도 가능한데)가 아닌 4회만 반복되었을 경우, 즉 4자리에만 요청을 받고 싶을 때는,
  
```python
urlpatterns = [
    path('', views.post_list),
    path('<int:pk>/', views.post_detail),
    
    # path('archives/<int:year>/', views.archives_year),
    re_path(r'archives/(?P<year>\d{4})/', views.archives_year),
  
    re_path(r'archives/(?P<year>20\d{2})/', views.archives_year),
]
    
```
  
- 이렇게 urls.py에서 {4}를 붙여주면 4번 반복이 된다. 그러면 3자리나 그 이상은 매칭이 되지 않고 꼭 4자리로 입력해야 매칭이 된다.  
  - 혹은 4자리에서 앞에 자리는 20으로 시작하게 하고 싶다면,  re_path(r'archives/(?P<year>20\d{2})/', views.archives_year), 이런식으로 작성한다.    (나는 왜 안되는지...)
  
- 아니면, 아예 커스텀해서 Converter를 만들수도 있다.

```python
from django.urls import path, re_path, register_converter
from . import views  
  
# 커스텀 Converter
class YearConverter:
    regex = r"20\d{2}"      # 정규표현식 패턴을 작성

    def to_python(self, value):
        return int(value)

    def to_url(self, value):
        return "%04d" % value  # 또는 return str(value)
  
  
# YearConverter를 Year이라는 이름으로 사용하겠다는 의미  
register_converter(YearConverter, 'year')  
  

urlpatterns = [
    path('', views.post_list),
    path('<int:pk>/', views.post_detail),
    path('archives/<year:year>/', views.archives_year),
]
```

- urls.py에서 Converter 관련 클래스를 정의해주자. to_python 함수는 -> url 매칭이 되었을 때, View 함수를 호출하기 전에 인자를 한 번 정리해주는 것이다. 원래 정규표현식을 거치면 인자가 문자열이 되는데, 그걸 지금은 정수로 변환해서 넘겨주게 된다.
  - to_url 함수는 -> url reverse 할 때 어떤 값을 url 문자열로 리버싱할 때 호출되는 함수이다.
  
- 실제로 클래스로 정의된 Converter를 사용하기 위해서는, urls.py 위에 from django.urls import path, re_path, register_converter 이렇게 import를 해줘야 한다.
  - 그리고 register_converter(YearConverter, 'year') 이렇게 YearConverter를 year이라는 이름으로 사용하겠다고 설정하기.
  - 그 다음에 path('archives/<year:year>/', views.archives_year), 이런식으로 Converter의 이름을 적어주면 된다.
  

* * *
  
## App 이름 설정하기
```python
app_name = 'instagram' 
  
urlpatterns = [
    path('', views.post_list, name="post_list"),
]      
```  
  
- 각각의 urlpattern 마다 name을 지정할 수 있다. 위에 코드처럼 path함수에 name을 지정하면, 이 post_list가 다른 App에도 있을 수가 있다. 그래서 해당 이름이 어떠한 App에 있는 이름인지 명시해줄 수 있다.
  - 앱 내부에 있는 urls.py에서 app_name = '앱이름' 이렇게 지정한다. url Reverse에서 namespace 역할을 하게 된다.
 
