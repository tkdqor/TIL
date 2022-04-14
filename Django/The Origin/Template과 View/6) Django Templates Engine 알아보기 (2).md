## Django Templates Engine 알아보기 (2)
- liongram 프로젝트로 작업환경 세팅하기


### 프로젝트 단위로 templates 관리하기
- 먼저 프로젝트 디렉터리 내부 settings.py로 가면 TEMPLATES라는 변수가 있다. 여기에 DIRS에다가 template의 경로를 넣어주면 된다.
- settings.py 위쪽에 보면 BASE_DIR라는 변수가 있는데 이게 루트 디렉터리 위치를 의미한다. 그래서,

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

- **이렇게 'DIRS': [BASE_DIR / 'templates'] 이런식으로 넣어준다. 즉, 워크스페이스(루트 디렉터리 위치)의 templates 위치를 의미한다. 그래서 liongram 안에서 새폴더를 만들고 templates라고
  이름을 지어주자.**
  - **그리고나서 templates 하위에다가 앱명인 posts로 디렉터리를 만들어주자. 그 다음에 posts 하위에는 post_list.html를 만든다.**
  - 그 다음 차례대로 post_detail.html, post_form.html, post_confirm_delete.html까지 복사해서 만들어준다.


### posts App 내부 Views.py 코드 추가하기
- Views 파일에 함수 추가하기

```python
def post_list_view(request):
    return render(request, 'posts/post_list.html')

def post_detail_view(request, id):
    return render(request, 'posts/post_detail.html')

def post_create_view(request):
    return render(request, 'posts/post_form.html')

def post_update_view(request, id):
    return render(request, 'posts/post_form.html')

def post_delete_view(request, id):
    return render(request, 'posts/post_confirm_delete.html')
```        
    
- **render 함수의 두번째 인자로 경로를 작성할 때 기준은 --> 우리가 위의 settings.py에서 설정한 DIRS 다음 경로를 여기에 작성해주면 된다. 지금은 DIRS가 루트 디렉터리 - templates로 되어있으니까 그 하위인 posts부터 render 함수에 작성해주면 된다.**
- 위에 코드처럼 우리가 생성한 html에 맞게 View 함수를 작성해주면 된다. **그리고 path에 id와 같은 변수가 있다면 --> def 함수의 파라미터에 id를 추가로 입력해줘야 한다.**



### urls.py 설정하기
- posts 앱 내부에 urls.py라고 해서 만들어주기

```python
from django.urls import path
from .views import post_list_view, post_create_view, post_update_view, post_detail_view, post_delete_view

urlpatterns = [
    path('', post_list_view),
    path('new/', post_create_view),
    path('<int:id>/', post_detail_view),
    path('<int:id>/edit/', post_update_view),
    path('<int:id>/delete/', post_delete_view),
]
```

- **우리가 정의한 View에 맞게 path를 5개 추가해주기. 그리고나서 앱 내부의 urls.py가 아닌 config 디렉터리 안에 있는 urls.py에 이걸 연결해줘야 한다.**

```python
from django.urls import path, include
...

urlpatterns = [
    path('admin/', admin.site.urls),
    path('url/', url_view),
    path('url/<str:username>/', url_parameter_view),
    path('fbv/', function_view),
    path('cbv/', class_view.as_view()),

    # posts 앱으로 연결 
    path('posts/', include('posts.urls')),
]
```

- **include 함수를 import 해주고 path를 추가해서 include('App명.urls') 이렇게 연결해주면 된다.**

- 여기까지 작성한 다음 브라우저에서 localhost:8000/posts/ 했을 때 목록 화면을 보여주고, 다른 url도 정상적으로 화면을 보여주는 것을 확인할 수 있다.
