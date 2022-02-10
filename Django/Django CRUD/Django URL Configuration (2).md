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
- **HTTP Method와 url pattern만 보더라도 client가 뭘 원하는지 명시적으로 알 수 있게끔 개발하는 것을 의미.**
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
- **그리고 여기에서 posts/ 로 시작하면 -> posts 앱의 urls.py로 보내는 것이기 때문에, posts 앱의 urls.py에서는 path('', views.index) 이렇게 비워두면 된다.**

- 이렇게, posts와 관련된 url pattern를 posts 앱 내부에서 별도의 urls.py로 분리해서 관리하면 -> posts/로 시작하는 다양한 url pattern에 대해서 훨씬 더 수월하게 관리할 수 있다.


### 게시물 번호에 따른 url 설정해주기
- 게시판에 게시글들이 많아지면, posts/10 또는 posts/20 이렇게 직접적으로 게시글의 번호를 일일이 지정할 수는 없을 것이다. 
- 그래서 사용자가 10을 입력했든, 20을 입력했든 url pattern 하나로 처리할 수 있게끔 할 수 있다.
  - **path함수의 첫번째 파라미터에 <>를 쓰고 변수를 적어준다. ex) <post_id>**
  - **그리고 이 변수가 숫자였으면 한다면, 변수의 type를 <int:post_id> 이런식으로 지정해줄 수 있다.** 그래서 post 앱의 urls.py에
```python
from django.urls import path
from . import views


urlpatterns = [
    path('', views.index),
    path('<int:post_id>/', views.detail),
    path('<int:post_id>/comments', views.comments),
]
```

- 이런식으로 url을 설정하게 되면, 어떤 숫자 형태의 id를 유동적으로 받아서 설정할 수 있게 된다.

- 이렇게 url pattern에다가 변수를 활용해서 다양한 값을 유동적으로 받을 경우에는 -> django가 해당 url pattern이 들어왔을 때 연동된 view 함수를 실행하게 된다.
  - 이 view 함수는 모두 첫번째 파라미터에 request를 무조건 입력해줘야 하고 / 위와 같이 url pattern에서 변수를 활용할 경우에는 -> view 함수의 두번째 인자로 해당 변수를 입력해줘야 한다.

```python
def index(request):
    return HttpResponse('Hello world!')
    

def detail(request, post_id):
    return HttpResponse(f'Post {post_id} detail')


def comments(request, post_id):
    return HttpResponse(f'Post {post_id} comments')
```

- detail과 comments view 함수에 post_id 변수를 파라미터로 설정하자. **즉, url에서 받은 숫자를 post_id라는 변수로 받아서 view 함수에 전달해주는 것이다.**
- 그리고 전달받은 변수를 함수 내부에서 활용하여 사용할 수 있다.

* * *
- 위에서 설정한 post_id는 변수이기 때문에 다른 이름으로 설정해도 상관없다.

```python
urlpatterns = [
    path('', views.index),
    path('<int:id>/', views.detail),
    path('<int:id>/comments', views.comments),
]
```

- urls.py 설정


```
def index(request):
    return HttpResponse('Hello world!')
    

def detail(request, id):
    return HttpResponse(f'Post {id} detail')


def comments(request, id):
    return HttpResponse(f'Post {id} comments')
```

- views.py 설정

