## URL Configuration 
- app를 여러 개 사용하게 되면, 프로젝트 urls.py에서 각 app의 views.py를 import 해야 되는데 이름 중복이 발생하게 된다. 이러한 문제를 해결하기 위해
```python
from posts import views as posts_views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('posts/', posts_views.index),
    path('', posts_views.index),
]
```

- 이런식으로 import 할 때 이름을 바꿔서 사용할 수는 있다. 하지만, 계속해서 url이 늘어나면 관리하기 힘들어진다.

- 그래서 django에서는 **URL Configuration** 이라고, URL 설정 파일을 django app 단위로 나눠서 별도의 파일로 관리하게 된다.


### Restful API
- HTTP Method와 url pattern만 보더라도 client가 뭘 원하는지 명시적으로 알 수 있게끔 개발하는 것을 의미.
- 만약 url pattern이 posts/ 라고 시작한다면 -> 이러한 url은 전부 post, 게시물과 관련된 url이라는 것을 확인할 수 있다. 즉, posts app과 관련된 url pattern이라고 볼 수 있다.


### 실습
- 먼저 posts 앱 내부에 urls.py을 생성하고
```python
from django.urls import path
from . import views


urlpatterns = [
    path('', views.index)
]
```

- 다음과 같이 작성해주자. 그리고 프로젝트 내부의 urls.py에서는,
```python
from django.contrib import admin
from django.urls import path, include
# from posts import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('posts/', include('posts.urls')),
]
```

- **include라는 함수를 추가로 import해서, posts/라는 url로 요청이 들어오면 include 함수를 사용해서 posts 앱에 있는 urls.py를 참고하라고 설정해주자.**
- 그리고 여기에서 posts/ 로 시작하면 -> posts 앱의 urls.py로 보내는 것이기 때문에, posts 앱의 urls.py에서는 path('', views.index) 이렇게 비워두면 된다.


