## Create로 데이터 생성하기 (2)
- 먼저 liongram 프로젝트에서 url 매칭을 해주자. 프로젝트 디렉터리 내부에 templates 디렉터리가 있고 그 안에 index.html이 있다.

- 우리가 서버를 구동시키고 브라우저를 새로고침하면, VSCode 터미널에 요청이 굉장히 많이 뜨게 된다. 
  - 브라우저에서 우클릭하고 개발자도구를 키면 -> 그리고 네트워크를 클릭하고 새로고침을 하면 파일들이 뜨게 된다. 우리가 localhost에게 Http 요청을 날린 것이다. -> 그래서 응답을 받아 html을 
    가져온 것이다. 그 안에서 CSS도 우리가 요청해서 받아온 것이다. 그래서 이렇게 한 번 요청했을 때 터미널에 많이 뜨게 되는 것이다. (파일들이 많으니까)
  - **근데, 다시 새로고침하면 터미널에 많이 뜨지는 않는다. 그 이유는, 이 페이지 자체에 일부 정적 데이터들은 우리가 캐싱을 하고 있다. 즉, 임시 보관을 하고 있게 된다. 그렇기 때문에 요청이 
    안 날라오게 되는 것이다.**
  - **근데 우리가 새로고침을 할 때, 현재 캐싱을 지우고서 새로고침을 하는 방법도 있다. 윈도우에서는 컨트롤 + shift + R이고 Mac에서는 command + shift + R이다. 그래서 이렇게 새로고침을 하면 다시 많은 요청이 날라온다.**


### urls 설정
- posts 앱 내부에 urls.py에서 name를 설정해주기.

```python
app_name = 'posts'

urlpatterns = [
    path('', post_list_view, name='post-list'),
    path('create/', post_create_view, name='post-create'),
    path('<int:id>/', post_detail_view),
    path('<int:id>/edit/', post_update_view),
    path('<int:id>/delete/', post_delete_view),
]
```

- 이렇게 설정하면 base.html에서 a element를 사용할 때, \<a href="{% url 'posts:post-create' %}">링크\</a> --> 이렇게 사용할 수 있다.


### template 설정
- 프로젝트 디렉터리 내부 templates 디렉터리 - posts 디렉터리 - post_form.html를 수정해보자. 여기서 form 태그를 추가한다.

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}글 입력{% endblock %}

<!-- content block -->
{% block content %}
<h1>Post 입력 화면</h1>
<form action="{% url 'posts:post-create' %}" method="POST">
    {% csrf_token %}
    <!-- 이미지 입력 -->
    <div>
        <label for="id_image">이미지</label>
        <input type="file" name="image" accept="image/*" id="id_image">
    </div>
    <!-- 내용 입력 -->
    <div>
        <label for="id_content">내용</label>
        <textarea name="content" id="id_content" cols="30" rows="10"></textarea>
    </div>
    <!-- 데이터 전송  -->
    <div>
        <input type="submit">
    </div>
</form>
{% endblock %}
```

- form의 method는 POST로, 데이터를 생성하는 것이니까 설정했다. 
- 그리고 첫번째 div에는 이미지를 위한 label과 input를 설정해준다. type은 file로 설정.
- 두번째 div는 글 내용을 입력하기 위한 코드이다. 여기서는 input이 아니라 textarea element를 사용한다. 우리가 Post 모델의 content 필드를 TextField로 했기 때문에 긴 글이 필요하다.
- 마지막 div는 데이터를 보내기 위해 input의 type를 submit으로 수정해준다.
- csrf_token은 --> 간혹 서버에서 form를 응답해준 게 아니고, 다른 사람들이 위조한 페이지가 응답되는 경우가 있다. 또는, 내가 의도치 않게 어떤 서버에게 요청을 날리게 되는 경우도 있다. 특정 서버로 보내게 만들어버린다. 그래서 그게 보내져서 로그인도 되고 다양한 행위에 대한 요청이 이루어진다. 그럼 서버에서는 그냥 응답해준다. 근데 사실 컴퓨터가 날린 것이지 그 사람이 날린 것인지는 모르기 때문에 위험하다. 이런 문제를 csrf_token으로 막아주는 것이다. 이렇게 csrf_token를 추가하고 브라우저 개발자도구를 보게 되면,

<img width="457" alt="image" src="https://user-images.githubusercontent.com/95380638/164242426-8138bf17-1165-4ee2-a5be-79647d578a61.png">

- **이렇게 input element가 hidden으로 생기게 된다. 즉, 서버가 응답해준 것이라고 해서 확인하는 토큰을 여기에 실어준 것이다.** 그래서 이 토큰이 들어가야 데이터 생성을 수행할 수 있다.


### View 수정하기
- post_create_view 같은 경우에는 GET 요청일 경우, 글 생성 페이지를 보여주고 POST 요청일 경우 글을 생성할 수 있는 기능을 구현해야 한다. 그래서 posts 앱 내부에 views.py에 가서 수정해보자.

```python
from django.shortcuts import render, redirect

...
def post_create_view(request):
    if request.method == 'GET':
        return render(request, 'posts/post_form.html')
    else:
        image = request.FILES.get('image')             # 이미지 파일을 받을 경우, request.FILES로 사용
        content = request.POST.get('content')
        print(image)
        print(content)
        return redirect('index')
```


- 이렇게 GET 요청인 경우 post_form.html를 랜더링 해주면 된다. 그리고 POST일 경우에는 지금만 redirect로 index페이지를 보여준다.
  - POST요청, 즉 입력된 데이터를 받는 코드를 작성해보자.
  - **이 때, image의 경우 --> 파일이기 때문에 reqeust.FILES 이렇게해서 받아줘야 한다.**
    - **그리고 이미지를 사용자로부터 받을 때는, form element에서 다른 방식으로 인코딩을 해줘야 한다. HTTP로 우리가 데이터를 통신하는데 데이터를 어떤 형태로 보내줄 것이냐에 대한 정의를 해줘야 한다. 데이터가 어떤 식으로 인코딩되서 날라갈 것인지 말이다. 우리가 구글이나 네이버에서 검색할 때, url에 퍼센트나 다른 문자들로 바뀌는 경우가 있다. 그게 인코딩되서 날아가는 형태인 것이다.**
    - 그래서 이미지 파일을 보낼 때는 인코딩 방식이 필요하다. 그래서 post_form.html를 보면,

```html
h1>Post 입력 화면</h1>
<form action="{% url 'posts:post-create' %}" method="POST" enctype="multipart/form-data">
...
```

- **form element에서 enctype라는 속성을 넣어주고, 우리가 파일을 입력할 때는 multipart/form-data 라는 것을 사용해줘야 한다.**


- 여기까지 하고나서 form에서 이미지와 텍스트를 넣고 데이터를 보내보면, 

```terminal
[20/Apr/2022 13:37:18] "POST /posts/create/ HTTP/1.1" 302 0
[20/Apr/2022 13:37:18] "GET / HTTP/1.1" 200 497
```

- **이런식으로 POST로 보내고 302번이 뜬다. 이건 제출 버튼을 눌렀을 때 우리가 redirect 해줬기 때문이다.**


* * *

### 데이터 생성하기
- Views.py에서 코드를 추가해서 데이터를 생성할 수 있게 해보자. post_create_view에서,

```python
...
def post_create_view(request):
    if request.method == 'GET':
        return render(request, 'posts/post_form.html')
    else:
        image = request.FILES.get('image')             # 이미지 파일을 받을 경우, request.FILES로 사용
        content = request.POST.get('content')
        print(image)
        print(content)
        Post.objects.create(image=image, content=content) # writer=request.user 추가 필요
        return redirect('index')
```

- **else 구문에서 Post 모델을 가져와서 create 함수를 사용하면 Post 모델에 데이터가 추가된다.**
  - **writer 필드는 지금 로그인된 유저를 뜻하는 request.user로 넣어준다.** 하지만, 실제로 지금 상태에서 데이터를 보내면 에러가 난다. 우리가 로그인을 하지 않았기 때문이다. 그래서 일단은 주석처리를 해주자.
  - 데이터를 보내면 데이터베이스에 추가가 될 것이다. 어드민 페이지에 들어가서 Post 모델을 보면 추가가 되어있다.
  - **참고로, request.user에서 user는 User 모델의 인스턴스, 객체이다.**


### 이미지 업로드 가능하게 하기
- 먼저 settings.py로 가서 static처럼 파일 설정을 추가해줘야 한다.
- **static은 서비스 제공자인 회사에서 업로드를 하는 파일이고 / media는 사용자들이 업로드 하는 파일이라고 보면 된다.**
- static 부분 밑에다가,

```python
STATIC_URL = 'static/'
STATICFILES_DIRS = [
    BASE_DIR / 'static',
]

MEDIA_URL = 'media/'
MEDIA_ROOT = BASE_DIR / 'media'
```


- **MEDIA_ROOT는 이 파일들이 어디에 들어가 있는지를 쓰는 것이다. STATICFILES_DIRS와 똑같다고 보면 된다.** 
  - 이렇게 설정하고 liongram 내부에 media라는 폴더를 만들어줘야 한다.

- **그리고나서 만약 사용자가 form에서 이미지를 업로드하고 요청을 날려야 할텐데, 그 설정이 필요하다.**
  - media 파일도 요청을 날려줘야 한다. config 디렉터리 내부에 urls.py에서 코드를 추가하자.

```python
...
from django.conf import settings      # settings.py 값들 가져오기
from django.conf.urls.static import static
...
# 이미지 파일 url 설정
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```


- **from django.conf import settings 이렇게 conf 패키지에서 settings를 import 해주면 --> settings.py에 있는 값들을 다 가져올 수 있다.**
- 또한, from django.conf.urls.static import static 이렇게 static도 가져와준다. **static() 함수는 static 파일 url 경로를 만들어주는 개념이다.**
- 그리고 urlpatterns에 추가를 해주는 것이다.
  - urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 이렇게 설정 해준다.

- **이렇게 설정한 다음, 그 전에는 이미지가 루트 디렉터리 내부에 그냥 쌓였다면 --> 이제는 media 디렉터리 내부에 쌓이게 될 것이다.**
  - 그리고 어드민 페이지에서도 이미지를 클릭하면 브라우저에 뜨게 된다.


* * *

### 로그인 되었을 경우에만 기능 가능하게 하기
- View에서 decorator를 사용하면 로그인이 되었을 경우에만 기능을 가능하게 할 수 있다. views.py의 post_create_view를 그렇게 설정해보자.

```python
from django.contrib.auth.decorators import login_required
...

@login_required                  # 로그인 되었을 경우에만 가능하게 하기
def post_create_view(request):
    if request.method == 'GET':
        return render(request, 'posts/post_form.html')
    else:
        image = request.FILES.get('image')             # 이미지 파일을 받을 경우, request.FILES로 사용
        content = request.POST.get('content')
        print(image)
        print(content)
        Post.objects.create(image=image, content=content, writer=request.user) # writer=request.user 추가 필요
        return redirect('index')
```

- **먼저 login_required를 import 해주고, @login_required 라는 것을 함수 위에 추가해주면 --> 해당 함수는 로그인을 했을 때만 처리를 해주는 함수로 바뀐다.**
- login_required를 command로 클릭해보면,

```python
def login_required(
    function=None, redirect_field_name=REDIRECT_FIELD_NAME, login_url=None
):
    """
    Decorator for views that checks that the user is logged in, redirecting
    to the log-in page if necessary.
    """
    actual_decorator = user_passes_test(
        lambda u: u.is_authenticated,
        login_url=login_url,
        redirect_field_name=redirect_field_name,
    )
    if function:
        return actual_decorator(function)
    return actual_decorator
```

- **이렇게 login_required라는 함수가 정의되어있다. 그래서 post_create_view 함수가 실행이 되기 전에 이 함수가 먼저 실행이 되는 것이다. 그래서 유저가 로그인 되었는지 먼저 확인하고, 안 되었다면 login_url이라고 해서 account/login이라는 url로 redirect 해준다.**
- 이렇게 설정하면, admin으로 로그인을 해야 생성 페이지로 들어갈 수 있게 된다.
