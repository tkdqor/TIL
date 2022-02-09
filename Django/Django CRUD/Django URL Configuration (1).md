## Django URL Configuration 
- django URL 설정 방법 
- 지금까지 urls.py에서 posts/라는 URL로 HTTP Request가 들어오게 되면, posts app에 있는 views 모듈의 index 함수가 처리하게끔 연결.

- django 프로젝트를 만들게 되면, 기본적으로 프로젝트 이름과 동일한 이름의 package가 생성된다.
  - 해당 package 내부에는 프로젝트 전반적인 설정과 관련된 파일들이 존재. ex). settings.py

- 프로젝트 디렉터리 내부에는 우리가 만든 posts App package가 존재한다.

- 프로젝트 디렉터리 내부에 있는 urls.py는 -> server가 서빙가능한 url의 목록을 관리하는 모듈이다. 여기에 등록되어있는 url pattern만 정상적으로 처리할 수 있다.
  - 안에 urlpatterns 리스트에는, path 함수를 사용하여 posts/라는 URL로 요청이 들어올 경우 -> views.py의 index 함수가 처리하게끔 설정했다.
  - views.py에는 index함수를 새롭게 정의. 

- django의 server를 처음 작동시켰을 경우, terminal에 Watching for file changes with StatReloader라는 메세지가 뜨게 되는데, django 프로젝트 내부에 있는 파일이 변경사항이 있는지 계속해서 모니터링하게 된다.
  - 그리고, django server가 실행된 시각 / django의 버전 / django server가 어떠한 설정파일을 기반으로 실행됬는지(프로젝트 내부 settings.py) 터미널에 출력하게 된다. 
 
 
### 개발환경
- 개발자들은 실제 서버를 배포하기 이전의 환경과 실제 서비스를 운영하기 위한 환경을 구분하기 위해 development 환경 / production 환경 이렇게 서버가 실행되는 환경을 구분해서 사용한다.
  - development 환경에서는 개발의 편의성을 위해 개발에 도움이 되는 각종 기능들을 다 켜놓은 상태에서 개발하게 된다. 그 결과, 성능은 조금 떨어지게 된다.
    - 그래서 우리가 파일을 수정하는 경우에 자동으로 server가 재시작된다. 이것을 위해 계속해서 파일의 변경사항을 모니터링 하는 것이다.
    - 다만, server가 실행된 이후에 생성된 파일에 대해서는 이를 인지할 수 없기에 재시작되지 않는다.
  - production 환경에서는 실제 서비스를 운영하기 위한 최적화된 서버를 구동해야 하기 때문에 실제 서비스를 운영하는데 불필요한 기능, 보안 상 이슈가 발생할 수 있는 기능들은 모두 끈 상태로 서버를 구동하게 된다. 

- 만약, urls.py에 없는 url로 접속하게 되면 Page not found라는 오류화면을 보여주게 되는데, 이 때 **URL conf는 URL 설정을 의미한다.**


### urlpatterns 리스트
- django에서는 기본적으로 host 뒤에 request URL를 적은 다음 /를 붙이지 않아도 자동으로 붙여준다고 했는데, 그 이유가 바로 urls.py에서 /를 붙인 상태로 urlpatterns를 정의해주고 있기 때문이다.
```python
from django.contrib import admin
from django.urls import path
from posts import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('posts/', views.index),
    path('', views.index),
]
```

- 만약, localhost:8000과 같이 host주소, 도메인만 적고 싶을 떄는 urls.py -> urlpatterns에서 별도의 url를 적지 않으면 기본적으로 도메인 다음에 /를 붙여주게 된다. 그래서 위와 같이 path 함수 첫번째 인자로 빈 문자열을 적어주면 된다.
  - 아니면 랜딩 페이지를 위한 별도의 App를 만들어서 그 App 내부에 있는 view를 가져와서 연동시켜도 된다.


### HTTP Request
- 우리가 웹페이지에 localhost:8000 주소로 enter를 입력할 때마다 HTTP Request가 발생하게 되고 그 때 마다 server에는(terminal에는) -> request가 발생한 시각과 어떤 method로 HTTP request가 발생했는지, 그리고 어떤 request URL을 요청했는지를 출력해준다. 200은 성공적으로 request가 처리되었다는 의미이다.
- 즉, 우리 서비스의 사용자가 우리의 서비스를 이용할 때마다 HTTP Request가 발생하는 것이고, HTTP Request가 발생할 때마다 server에서 기본적으로 기록을 남겨주는데, 이러한 기록을 **server 로그**라고 부른다.
  - 이러한 로그는 우리가 따로 개발하지 않아도 django가 기본적으로 자동으로 남겨주는 로그인데, 원한다면 우리가 해당 request를 전달받았을 때 필요에 따라 로그를 직접 남길 수도 있다.

```python
def index(request):
    print('posts URL 패턴 로그')

    return HttpResponse('Hello world!')
```

- 이런식으로 print 함수를 사용해서 우리가 새로고침 할 때 마다, view index 함수가 실행되면 터미널에 해당 문자열을 출력해줄 수 있다. 문자열말고도 변수 등을 print의 인자로 넣어서 문제를 확인해볼 수 있다.
