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
    path('accounts/', include('accounts.urls')),
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
  - **django의 url configuration에서는 url pattern만 정의할 수 있을 뿐, HTTP Method에 대한 부분은 설정할 수 없다. 그래서 GET방식이 되었든 POST방식이 되었든 sign_up이라는 url pattern은 sign_up View 함수가 처리하고 login이라는 url pattern은 login이라는 View 함수가 처리하게 된다.**

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

- 다음과 같이 error라는 데이터를 보내주는 것은 회원가입에 실패했을 경우에 보내줄 메시지를 담은 변수이다. 
- 그리고 회원가입을 하는 데 필요한 아이디, 비밀번호, 비밀번호 확인을 위한 3개의 input element를 추가했다. 비밀번호와 비밀번호 확인은 type이 password로 되어있다. 이 경우에는 뭔가를 입력하면 화면에 가려져서 보이게 된다.
  - server에 데이터를 전송할 때 각각의 데이터가 어떤 데이터인지 name attribute를 이용해서 이름을 붙여줬다.

- 그리고 POST 방식으로 accounts 앱의 sign_up이라는 이름의 url pattern으로 HTTP Request를 전송하게 된다.
  - POST 방식으로 form을 사용하니까 csrf_token도 추가를 했다.


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

        <h1>Login</h1>

        <form method="POST" action="{% url 'accounts:login' %}">
            {% csrf_token %}

            <div class="mb-3 col-md-6">
                <label class="form-label" for="username">아이디</label>
                <input class="form-control" id="username" type="text" name="username" placeholder="아이디">
            </div>

            <div class="mb-3 col-md-6">
                <label class="form-label" for="password">비밀번호</label>
                <input class="form-control" id="password" type="password" name="password" placeholder="비밀번호">
            </div>

            <div class="mb-3">
                <button type="submit" class="btn btn-primary">로그인</button>
            </div>
        </form>
    </div>

{% endblock %}
```

- 위에는 login.html 코드이다. sign_up.html과 유사한데 아이디와 비밀번호만 받는 input element로 구성되어있다. 

* * *

- 다음은 sign_up View 함수와 login View 함수이다.

```python
from django.shortcuts import render, redirect
from django.contrib.auth.models import User    # django에 내장된 User 모델
from django.contrib import auth                # django에 내장된 auth 모델

# 회원가입 기능 
def sign_up(request):
    context = {}

    if request.method == 'POST':
        if (request.POST.get('username') and 
            request.POST.get('password') and
            request.POST.get('password') == request.POST.get('password_check')):

            new_user = User.objects.create_user(
                username = request.POST.get('username'),
                password = request.POST.get('password'),
            )

            auth.login(request, new_user)

            return redirect('posts:index')

        else:
            context['error'] = '아이디와 비밀번호를 다시 입력해주세요.'

    return render(request, 'accounts/sign_up.html', context)    
    
    
    
# 로그인 기능
def login(request):
    context = {}

    if request.method == 'POST':
        if request.POST.get('username') and request.POST.get('password'):

            user = auth.authenticate(
                request,
                username = request.POST.get('username'),
                password = request.POST.get('password'),
            )

            if user is not None:
                auth.login(request, user)
                return redirect('posts:index')
            else:
                context['error'] = '아이디와 비밀번호를 다시 입력해주세요.'

        else:
            context['error'] = '아이디와 비밀번호를 모두 입력해주세요.'     

    return render(request, 'accounts/login.html', context)        
```

- 이렇게 작성하게 되면 일단, GET방식으로 accounts/sign_up 또는 accounts/login이라는 HTTP Request를 주게 되면 회원가입 및 로그인 페이지가 나온다.
- **View 함수에서 -> GET방식일 때와 POST방식일 때를 구분하기 위해서 HTTP Method 정보를 담고있는 request.method를 활용해서 POST방식이 맞는지 if문을 사용했다.**
  - 만약 POST방식이 아니면 바로 return으로 template 페이지를 랜더링하도록 했다.

- 회원가입 페이지에서 아이디와 비밀번호, 비밀번호 확인을 입력하면 -> sign_up View 함수에서는, 해당 request가 POST 메소드일 때 request.POST.get(), POST방식으로 전달된 데이터 속에서 username이라는 데이터와 password라는 데이터가 실제로 있는지 확인하고 만약 없으면 None이 return 된다. 그리고 password와 password_check가 서로 같은지까지 확인한다.
  - 그리고 if문에서 조건이 길 때는 -> 모든 조건을 감싸는 괄호를 작성해줘야 한다. 그러면 여러 줄에 걸쳐서 작성할 수 있다. 만약 조건을 만족하지 않는다면 -> else문의 context['error'] = '아이디와 비밀번호를 다시 입력해주세요.' 가 실행되서 context 딕셔너리에 'error'라는 key로 에러 메세지를 저장하게 된다. 그리고 else문에는 별도의 return이 없기 때문에 에러 메시지를 저장한 다음, 그 아래에 render 함수가 실행된다. 그래서 context가 전달되어 template에는 에러 메세지가 뜨게 된다.

- 만약, 회원가입 페이지에서 정상적으로 아이디, 비밀번호, 비번 확인 값을 입력하게 된다면 -> **django User 모델에 내장되어있는 User.objects.create_user라는 함수를 통해서 새로운 유저를 추가할 수 있다. (왜 User 모델에서만 이렇게 create함수를 쓰는걸까?)** -> 이 과정이 회원가입이다.
  - **django User 모델을 활용해서 새로운 사용자를 추가할 때 비밀번호를 암호화해서 저장한다든지 등의 특별한 절차들이 수행되어야 하기 때문에 사용자를 추가할 때에는 반드시 이 create_user 함수를 사용해야 한다.**
  - 이 create_user 함수가 실행되면 해당 데이터를 바탕으로 데이터베이스에 실제로 추가해주게 된다. 그래서 별도의 save 함수가 필요없다.

- **회원가입이 완료가 되면 -> django auth 모듈이 login 함수를 통해서 해당 유저를 로그인시켜줄 수 있다.**
  - **첫번째 인자로는 모든 view 함수가 가지는 request를 입력하고, 두번째 인자로는 로그인을 시켜주고자 하는 사용자를 전달해주면 된다. 이렇게 로그인이 가능하다.**
  - 로그인 처리가 된 다음으로는 redirect함수로 다른 template으로 보내준다.

* * *

- login View 함수는, POST 방식일 때 username과 password 데이터가 모두 입력되어야 하고, 값이 하나라도 입력되지 않으면 context 딕셔너리에 에러메세지를 담은 key를 저장한다.
- **username과 password를 모두 입력했다면 -> django의 auth 모듈이 authenticate라는 함수를 통해서 해당 username과 password로 가입된 유저가 있는지 체크하게 된다.**
  - **authenticate 함수를 사용할 때는 첫번째 인자로 모든 View 함수가 가지고 있는 request를 전달하고, 그 다음 인자들은 인증을 하고자 하는 사용자의 username 정보와 password 정보를 인자로 전달해주면 된다.**
  - **그래서 이 authenticate 함수를 통해 인증된 사용자가 있을 경우에는 -> 함수의 결과로 해당 유저를 담고있는 user 인스턴스가 return이 된다. 만약, 인증에 실패할 경우에는 None를 return 하게 된다. django User 모델에 유저가 추가될 때에는 우리가 비밀번호를 1234라고 저장해도 실제 데이터베이스에는 그냥 1234가 아니라 암호화된 복잡한 문자열이 저장되기 때문에 우리가 직접 값을 비교할 수가 없다.**
  - 그래서, get 함수나 filter 함수로 password가 1234인 유저를 찾는 코드를 작성할 수 없다. 반드시 authenticate라는 특별한 함수를 사용해야 한다. 

- **그래서 authenticate 함수의 실행결과로 -> user라는 변수에 user 인스턴스가 담기거나, None이 담길 것이기 때문에 if user is not None: 이라는 코드로 인증된 사용자 정보가 있을 때를 의미한다. 있다면 auth모듈의 login 함수를 통해서 해당 유저를 로그인 시켜주는 것이다.** 
  - 만약 user가 None이라면, 조건을 만족하지 않으므로 정확하지 않은 값이 입력되었다는 것이니까 다시 입력하라는 메세지를 담은 key를 저장하게 된다.

* * *

### 쿠키와 세션
- 원래 사용자 인증을 직접 구현하기 위해서는, 쿠키와 세션이라는 개념에 대해서 공부해야 한다. 하지만 django를 활용하면 이런 개념을 몰라도 지금처럼 쉽게 인증을 구현할 수 있다.

- 사용자가 아이디와 비밀번호를 server에 전송하면서 로그인을 요청했을 때, 해당 아이디와 패스워드를 가진 사용자를 먼저 찾고 해당 사용자의 계정이 있다면 로그인을 해주는 건데, 보통 로그인을 하면 한 번 로그인을 했을 때 다른 페이지로 계속 이동하더라도 계정 정보가 쭉 유지되는 걸 생각하게 된다. 
  - **하지만, HTTP 통신은 Stateless라고 해서 각각의 HTTP Request가 모두 다 독립적이다. 즉, 우리가 로그인 페이지에서 아이디, 비밀번호를 server에 전송하고 게시물 목록 페이지나 다른 상세 페이지를 요청하더라도, server 입장에서는 이 모든 request를 독립적으로 판단하기 때문에 -> 우리가 직전에 로그인을 했는지 어떤지 관심도 없고 파악할 수 있는 방법도 없다.** 
  - 그런데, 일반적으로 로그인이 되어있으면 화면 상단에 마이페이지, 로그아웃 이런식으로 로그인이 되어있으면 다른 페이지로 이동하더라도 계속 사용자의 계정 정보를 확인할 수 있다. 이와 같은 기능을 구현하기 위해서 매번 모든 HTTP Request에서 사용자를 확인하게 된다. 
  - 우리가 가진 계정으로 로그인을 시도하면 -> auth모듈의 login 함수를 통해서 django한테 로그인을 처리해줘 라고 부탁하게 되는데, 이 때 django는 server 어딘가에 이 로그인을 시도한 계정의 사용자를 식별할 수 있는 정보인 세션을 저장하게 된다. 그리고 해당 Request를 전송한 유저가 어떤 세션과 매핑되는지에 대한 정보를 쿠키라고 불리는 아주 작은 데이터로 브라우저 내에 심는다. 그래서 이 쿠키는 우리가 로그아웃을 하거나 자체적인 쿠키의 만료 시간이 모두 지났을 경우에 자동으로 소멸하게 된다.
  - 브라우저 내에 쿠키가 있을 경우, 우리가 request를 전송할 때 이 쿠키를 포함시켜서 전송하고 싶어 이렇게 하지 않아도 자동으로 모든 HTTP Request에 이 쿠키정보가 포함돼서 전송이 되게 한다. 그래서 server 입장에서는, 모든 request를 받을 때마다 세션 정보를 담고 있는 쿠키가 있는지 없는지 확인하고 -> 만약에 있다면 이전에 로그인을 한번이라도 수행했던 사람인 것이고, 로그아웃을 했거나 시간이 만료되어서 쿠키가 삭제되었다면 해당 request에 쿠키가 포함되어 있지 않을 것이고 server 입장에서는 로그인을 하지 않은 사람으로 간주하게 된다. 


### 로그인 여부 표시하기
- 로그인을 했다면 상단에 아이디와 함께 로그아웃 버튼을 제공하고, 로그인이 되어있지 않다면 로그인버튼과 회원가입 버튼을 제공하자. **그래서 로그아웃을 위한 POST 방식의 url pattern이 필요하다.**
  - **만약 GET 방식으로 로그아웃을 구현하게 되면, 브라우저의 주소를 잘못 입력하는 것만으로도 로그아웃이 될 수 있기 때문에 POST 방식으로 요청이 들어올 때만 로그아웃을 시켜주도록 해보자.**

```python
from django.urls import path
from . import views

app_name = 'accounts'

urlpatterns = [
    path('sign_up/', views.sign_up, name='sign_up'),
    path('login/', views.login, name='login'),
    path('logout/', views.logout, name='logout'),
]
```

- urls.py에서 로그아웃 버튼을 누르면 요청될 url pattern를 하나 추가해준다.

```python
# 로그아웃 기능
def logout(request):
    if request.method == 'POST':
        auth.logout(request)

    return redirect('posts:index')   
```

- 그리고 logout이라는 View 함수를 만들어준다. 실제 로그아웃 처리는 auth 모듈의 logout 함수를 사용하기만 하면 된다. auth.logout 함수를 통해서 쿠키와 세션 정보를 초기화하는 것이다. 그러면 브라우저나 server에 사용자 정보가 남지 않게 된다.

- 이제 로그인 버튼과 로그아웃 버튼이 노출될 수 있게끔 하자.
```html
<div id="navbar-content-right">
  <!-- 로그인 되었을 때 표시 및 로그아웃 버튼 생성 -->
    {% if user.is_authenticated %}
      <form method="POST" action="{% url 'accounts:logout' %}">
         {% csrf_token %}

         {{ user.get_username }} 님 환영합니다.
          <button type="submit" class="btn btn-primary">로그아웃</button>
      </form>
    {% else %}    
       <li class="navbar-content"><a href="{% url 'accounts:login' %}">로그인</a></li>
       <li class="navbar-content"><a href="{% url 'accounts:sign_up' %}">회원가입</a></li>
    {% endif %}    
</div>
```

- **위의 코드를 보면, django template language에서 user라는 변수를 사용하고 있는데, 원래 우리가 이러한 변수를 사용하기 위해서는 View에서 context로 데이터를 전달해줘야만 변수를 활용할 수 있었는데 -> django의 User모델은 특별한 기능이라서 따로 context에 유저 정보를 담아서 전달하지 않아도 template에서 바로 user 변수를 사용할 수 있다.**

- **user.is_authenticated**라는 코드는 -> 현재 사용자가 로그인되어있는 경우에는 True를 return하는 Boolean 값이다. 그렇지 않을 때는 False를 return 하게 된다. 그리고 user.get_username 코드를 통해 실제 유저의 아이디를 출력해줄 수 있다. 또한, 로그인이 되면 button를 생성해주자.
  - **django 유저의 username를 확인할 때에는 get_username이라는 함수를 사용하면 된다. django template language에서는 괄호를 사용하지 않고 함수를 호출한다.**

- 로그아웃 버튼을 눌렀을 때, POST 방식으로 accounts app의 logout이라는 url pattern으로 request가 전송되면서 로그아웃 처리가 되는 것이다.

- **로그인이 되었을 경우에는, 해당 계정 정보를 담고있는 세션이라는 데이터가 저장되고 브라우저에는 그 세션에 대한 정보를 담고 있는 쿠키라는 게 브라우저 내부에 저장되기 때문에 우리가 다른 페이지로 이동하면서 다양한 request를 전송하더라도 -> 항상 그 로그인 쿠키 정보가 같이 server에 전달되기 때문에 django server는 모든 request에 대해서 그 쿠키 여부를 검증해서 로그인했구나, 안 했구나를 구별할 수 있게 되는 것이다.** 
