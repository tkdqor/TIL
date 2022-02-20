## Authentication
- Authentication이란, 로그인과 같은말이라고 보면 된다. 서비스를 이용하는 사람이 실제 서비스에 등록되어 있는 사람인지를 인증하는 절차이다.
- 일반적으로 사용자는 본인이 누구인지를 밝히기 위해서 server에 아이디와 패스워드를 전달하게 된다. 그러면 server는 해당 아이디와 패스워드를 가진 유저가 User 모델에 있는지 확인하고 있으면 로그인 처리를 통해 정상적인 서비스 이용이 가능하게끔 한다.
- 그래서 먼저 DB에 사용자 데이터를 의미하는 User model 테이블을 만들어야 하고 어떤 인증 체계에서든 필수적으로 아이디와 패스워드를 요구하게 된다.
  - ID는 특정 유저를 식별하기 위해 필요한 최소한의 정보이다. 그리고 그 유저가 맞는지 패스워드까지 요구하게 된다.

- django는 이러한 인증 체계를 위해서 User 라는 모델을 제공한다. 그 모델에는 username, password, email, first_name, last_name 등의 필드를 사전에 정의해주었기 때문에 우리는 이 틀에 맞춰 사용하면 된다.
  - 여기서 username은 우리가 회원가입 시 적었던 ID라고 생각하면 된다. 따라서 django의 User 모델을 사용할 때 반드시 username과 password라는 필드를 사용해야 한다. 다른 정보들은 회원가입 시 받아도 되고 안 받아도 된다.


### 회원가입과 로그인
- 먼저 회원가입은, User 테이블에 새로운 유저를 추가하는 것이다. 
- 그리고 로그인은, User 테이블에 있는 사용자 중에서 전달받은 username과 password를 가진 사용자가 있는지 확인하는 것이 로그인이다. 
- 이러한 회원가입과 로그인은 함수형태로 이미 django에서 만들어뒀기 때문에 이걸 가져다가 쓰면 된다.


### 실습
- 먼저, 계정을 관리하기 위한 accounts라는 app를 하나 만들고 settings.py에 app를 등록해주자.
```terminal
python manage.py startapp accounts
```

- 그리고 이제 회원가입을 할 수 있는 Form를 제공하는 템플릿과 실제 회원가입을 하게 하는 view 로직을 구성해야 하고, 로그인을 할 수 있는 Form를 제공하는 템플릿과 실제 로그인을 하게 하는 view 로직을 구성하면 된다.
  - 또한, 회원가입 페이지와 로그인 페이지는 각각 GET /accounts/sign-up, GET /accounts/login 이라는 url로 설정하고 
  - 실제 회원가입과 로그인이 처리되는 로직의 경우에는 동일한 url pattern를 사용하되 메소드만 POST 방식으로 변경해서 사용해보도록 하자.  -> **Restful API를 생각해보자.**

```python
urlpatterns = [
    # admin 페이지 url
    path('admin/', admin.site.urls),
    # host 주소로 요청 시 posts앱의 main 페이지로 연결
    path('', views.main, name='main'),
    # posts/로 요청 시 posts 앱으로 연결
    path('posts/', include('posts.urls')),
    # accounts로 요청 시 accounts 앱으로 연결
    path('accounts/', include('accounts.url')),
]
```

- 먼저 프로젝트 디렉터리 내부 urls.py에서 위와 같이 accounts/로 요청되는 url이면 accounts 앱으로 연동시키자. 그리고 accounts 앱 내부에 urls.py를 생성한 다음

```python
from django.urls import path
from . import views

app_name = 'accounts'

urlpatterns = [
    path('sign_up/', views.sign_up, name='sign_up'),
    path('login/', views.login, name='login'),
]
```

- app_name을 설정하고 accounts/ 이후에 url pattern를 작성해준다. 각각 회원가입과 로그인 url로 설정하자.

- 그리고 이제는 회원가입 페이지 & 로그인 페이지를 form를 이용해서 보여주는 template과 회원가입 & 로그인을 해주는 로직을 구성한 View 함수를 만들어야 한다.

* * *
- 먼저 accounts 앱 내부에 templates 디렉터리를 만들고, 그 안에 accounts라는 이름의 디렉터리를 또 만들어서 sign_up.html과 login.html를 만든다.
- sign_up.html에는

```html
{% extends 'base.html' %}

{% load static %}

{% block style %}
{% endblock %}

{% block content %}

    <div class="container">
        {% if error %}
        <div class="alert alert-danger mt-3">
            {{ error }}
        </div>
        {% endif %}

        <h1>Sign up</h1>

        <form method="POST" action="{% url 'accounts:sign_up' %}">
            {% csrf_token %}

            <div class="mb-3 col-md-6">
                <label class="form-label" for="username">아이디</label>
                <input class="form-control" id="username" type="text" name="username" placeholder="아이디">
            </div>

            <div class="mb-3 col-md-6">
                <label class="form-label" for="password">비밀번호</label>
                <input class="form-control" id="password" type="password" name="password" placeholder="비밀번호">
            </div>

            <div class="mb-3 col-md-6">
                <label class="form-label" for="password_check">비밀번호 확인</label>
                <input class="form-control" id="password_check" type="password" name="password_check" placeholder="비밀번호 확인">
            </div>

            <div class="mb-3">
                <button type="submit" class="btn btn-primary">회원가입</button>
            </div>
        </form>
    </div>

{% endblock %}
```









