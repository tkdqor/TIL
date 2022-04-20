## Read로 데이터 조회하기
- 글 데이터를 list로 보여주기
- **views.py에 우리가 post_list_view라고 list를 만들기 위한 View를 설정했다. 여기서 코드를 입력하자.**

```python
...
def post_list_view(request):
    post_list = Post.objects.all()
    context = {
        'post_list': post_list,
    }

    return render(request, 'posts/post_list.html', context)
```

- 위의 코드처럼 Post 모델 데이터를 전부가져와서 변수를 넘겨주기.

- **그 다음은, post_list.html로 가서 보내준 변수를 활용한다.**

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}글 목록{% endblock %}

<!-- content block -->
{% block content %}
<h1>Post 목록 화면</h1>

{% for post in post_list %}
    <div>
        {{ post.id }}
        {{ post.image }}
        {{ post.content }}
        {{ post.created_at }}
        {{ post.writer }}
    </div>
{% endfor %}

{% endblock %}
```

- 이렇게 보내준 변수 post_list를 for문을 사용해서 하나씩 꺼내서 출력해볼 수 있다. post를 하나로 꺼내서 Post 모델의 필드를 출력할 수 있다.


### django ORM filter 사용해보기
- 다시 views.py의 post_list_view로 돌아가자. 

```python
def post_list_view(request):
    # post_list = Post.objects.all()   # Post 전체 데이터 조회
    post_list = Post.objects.filter(writer=request.user)    # Post.writer가 현재 로그인된 경우 조회
    context = {
        'post_list': post_list,
    }

    return render(request, 'posts/post_list.html', context)
```

- **이렇게 post_list = Post.objects.filter(writer=request.user) filter를 사용해서 --> Post 모델 데이터 중, 지금 내가 로그인한 유저와 작성자가 같은 경우를 조회해주는 것이다.**
  - **그래서 코드를 이렇게 설정하고 어드민 페이지에서 로그인한 뒤, 브라우저를 보면 지금 로그인된 유저가 작성한 글만 볼 수 있게 된다.**

- 이렇게 리스트를 출력해볼 수 있다.

* * *

### django debug toolbar 설치해보기
- 구글에 django debug_toolbar를 검색해보면, https://django-debug-toolbar.readthedocs.io/en/latest/installation.html 이렇게 공식 문서를 확인할 수 있다.
- 이 공식 문서에 따라서, 처음에는 패키지를 설치해줘야 한다.

```terminal
python -m pip install django-debug-toolbar
```

- 이 명령어로 설치를 해준다. 그 다음으로, settings.py에 몇몇 사항들을 추가해줘야 한다.

```python
INSTALLED_APPS = [
    ...
    'django.contrib.staticfiles',
    'debug_toolbar',
]

...

STATIC_URL = 'static/'

...
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]

...
INTERNAL_IPS = [
    # ...
    "127.0.0.1",
    # ...
]
```

- **여기서 'django.contrib.staticfiles' 는 우리의 static 파일이나 media 파일을 관리해주는 django 내장 패키지라고 생각하면 된다. debug toolbar도 이걸 활용하게 된다.**
  - 그리고 "debug_toolbar" 를 추가해준다.
- settings.py에서 MIDDLEWARE에도 추가해줘야 한다.
- 그리고 해당 파일 마지막에는 INTERNAL_IPS도 설정해준다.




- **그 다음으로는, urls.py도 추가해줘야 한다. 프로젝트 디렉터리 내부 urls.py로 가서**

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('url/', url_view),
    path('url/<str:username>/', url_parameter_view),
    path('fbv/', function_view),
    path('cbv/', class_view.as_view()),

    # index 페이지 url
    path('', index, name='index'),
    # posts 앱으로 연결 
    path('posts/', include('posts.urls')),

    # django debug toolbar URL 설정
    path('__debug__/', include('debug_toolbar.urls')),
]
```

- 이렇게 코드를 추가해준다.

- **여기까지가 공식 문서에 나와있는 내용이고 다시 서버를 구동시켜보자. 그러면 브라우저 화면 우측에 갈다란 버튼이 생기게 된다.**
  - 여기서 히스토리도 볼 수 있고, 버전, 시간 등등 여러가지를 볼 수 있다. Headers는 HTTP 관련된 내용이다. 

- **그래도 가장 많이 사용되는 기능은 --> SQL 기능이다.**
  - 우리가 post_list_view라는 함수를 한 번 돌리면서 실행된 SQL문이라고 보면 된다.
  - **SELECT ••• FROM "django_session" 이렇게 되어있는 부분은, 지금 로그인이 되어있는지 확인하는 sql문이라고 보면 된다. 그래서 우리가 로그인을 하고 뭔가 요청을 한 번 날릴때마다 이 쿼리가 무조건 돌게 된다.**
    - **물론 나중에는 이러한 인증 절차를 데이터베이스 기반으로 사용하지 않고, 캐싱 서버를 쓴다든지 파일 시스템을 써서 처리를 할 수 있는데, 지금 django는 기본적으로 인증 절차를 데이터베이스 기반으로 하기 때문에 이렇게 쿼리문이 날라가게 된다.**
    - **이러한 sql문은 우리가 views.py에서 post_list = Post.objects.all() 이런식으로 django ORM API를 날렸을 때마다 생성이 된다.**
    - **...all()과 post_list = Post.objects.filter(writer=request.user) 이 코드를 둘 다 활성화시키고 새로고침을 해보면, all이 없는 것으로 보인다. django ORM에서는 ORM 캐싱이라는 것을 제공해준다. 그래서 처음에 all()이라는 것을 사용했지만, 만약 실질적으로 지금 사용이 되지 않았다면 그냥 데이터를 갖고 있다가 나중에 실제 사용될 때 그걸 쿼리를 최소한 적게 날리기 위해서 저장하는 과정이라고 볼 수 있다.**

- post_list = Post.objects.filter(writer=request.user) 이 코드를 sql에서 확인하면 --> SELECT ••• FROM "auth_user" WHERE "auth_user"."id" = '1' LIMIT 21 이런식으로 쿼리문이 구현되어 데이터베이스에 요청이 가게 된다.


### index View 수정하기
- **이제는 views.py에 index 함수를 수정해보자.**

```python
...
# index 페이지 연결하기
def index(request):
    post_list = Post.objects.all()   # Post 전체 데이터 조회
    context = {
        'post_list': post_list,
    }
    
    return render(request, 'index.html', context)
```

- 이렇게 index 함수 내부에 Post 데이터를 전체 조회해준다. 


### index.html 수정하기
- **그 다음에는 index.html로 가보자. index.html로 가면**

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}홈{% endblock %}

<!-- content block -->
{% block content %}
<main>
    <div class="card-list">
        {% include 'mixin/posts/post_card.html' %}
    </div>
</main>
{% endblock %}
```

- **이렇게 include를 사용해서 post_card.html를 가져온다. 그래서 post_card.html를 들어가서 코드를 수정해준다.**

```html






- card의 header나 body 부분에 이미지도 post 인스턴스를 사용해서 출력한다.
