## Django App의 필요성
- 현재 프로젝트에서 사용하는 App, 예를 들어 블로그 기능을 다른 프로젝트에서도 사용하려고 한다면 매번 새롭게 만들수는 없다. 그래서 그 블로그 기능을 장고 App의 형태로 격리해서 만들어둔다면, 다른 프로젝트에도 적용하기가 편리하다. 그냥 폴더만 복사하면 끝이다. 복사하고 세팅을 조절하면 바로 이용할 수 있는 형태가 된다.
- 프로젝트 안에 들어와 있는 상태에서, 터미널에 
```terminal
python manage.py startapp 앱이름
```

- 이렇게 입력하면, 기본 App이 생성된다. 이것은 env -> lib -> python3.9 -> site-packages -> Django -> conf -> app_template 여기로부터 복사가 되어 생성되는 것이다.
- 명령어는 manage.py을 가지고 있는 디렉터리에서 입력하는 것이다.

* * *
- Django app의 필요한 이유는 바로 **재사용성**에 있다. 재사용성을 목적으로 한 것이 python package이다. 
- 만약, 재사용성을 목적으로 둔 게 아니라면, 하나의 App에서 현재 프로젝트의 모든 기능을 구현할 수 있다. 즉, App를 하나의 작은 서비스라고 생각해도 된다. 따라서 처음 연습할 때는 하나의 app에서 다 구현해보자.
- 하나의 App 이름은 현재 프로젝트 상에서 유일해야한다.
- 새롭게 생성한 장고앱이나 외부 라이브러리 형태의 장고앱은 필히 settings.INSTALLED_APPS 에 등록시켜야 장고앱으로서 대접을 받는다.


## 블로그 App 만들어보기
```python 
python manage.py startapp blog1
```

- 이렇게하면 blog1 이라는 이름을 가진 디렉터리가 생성된다. 그 디렉터리 안에는 migrations 디렉터리 / 던더init던더 파일 / admin파일 / apps파일 / models파일 / test파일 / views파일이 생성된다.
- 그리고 이 blog1 app 디렉터리안에 urls.py라는 파일을 생성하자.

```python
urlpatterns = [
    
]
```

- urls.py 파일 안에는 다음과 같이 리스트를 만든다.


```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog1',
]
```

- 그리고나서 settings.py 파일에 들어가서 -> 이렇게 우리가 만든 앱을 등록시킨다. 앱의 URLConf 를 제외한 많은 부분(모델, 템플릿, static 등)들이 자동으로 등록된다.


### models.py 설정
```python
from django.db import models     # models 패키지 import

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

- 먼저 models.py를 구성해보는데, 맨 윗줄에 자동적으로 db 패키지안에 models 패키지를 import 해온다. 그리고 Post라는 클래스를 정의하고 그 안에, title / content / created_at / updated_at 이라는 4개의 필드를 생성해보자.
- 이렇게 구성한 데이터베이스를 저장하기 위해서

```terminal
python manage.py makemigrations blog1
python manage.py migrate blog1
```

- 터미널에 다음과 같은 명령어를 입력하자. 그러면 이제 실제 데이터베이스에 Post라는 이름의 테이블이 생성된 것이다. db.splite3라는 파일이 데이터베이스 파일이다. 이 파일은 지우면 절대 안된다.

- 이제 어드민 페이지에 다시 들어가보면, 자동으로 테이블이 보여지지는 않는다. 어드민 페이지에서 보이게끔 하려면 -> 앱 내부에 있는 admin.py에다가

```python
from django.contrib import admin
from .models import Post       # 현재 디렉터리에 있는 models.py에서 Post 클래스를 import

admin.site.register(Post)      # Post 모델 어드민 페이지에 등록
```

- 다음과 같이 코드를 입력해주고 저장하기. contrib 패키지에 admin 앱을 가져오고 / 같은 디렉터리에 있는 models.py의 Post 클래스를 Import 하기. 그리고 Post 모델을 어드민 페이지에 등록해준다.
- 그리고나서 어드민 페이지를 다시보면, 앱의 이름과 Post 테이블이 보여진다.
- 해당 Post 테이블의 Add를 클릭하고 title과 content를 채우고나서 save를 눌러주면 저장이 된다.


### urls.py 설정
- 먼저, 프로젝트 디렉터리 안에 있는 urls.py를 선택하자.

```python
from django.contrib import admin
from django.urls import path, include      # include를 새롭게 추가

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog1/', include('blog1.urls')),   # 새롭게 url를 만들어주고 해당 url은 blog1 앱의 url로 보내주기
]
```

- **Django는 URLconf(URL configuration)를 사용하는데, URL를 어떻게 구성하는지에 대한 내용이고 즉, URLconf는 장고에서 URL과 일치하는 뷰를 찾기 위한 패턴들의 집합이다.**

### views.py 설정
- blog1 앱 내부에 있는 views.py로 가보자.

```python
from django.shortcuts import render    # django -> shortcuts.py -> render라는 함수 Import

def post_list(request):
    return render(request, 'blog1/post_list.html', {
    })
```

- views.py에서 먼저 django 패키지 -> shortcuts.py -> render라는 함수를 Import 하고 post_list라는 함수를 생성하자. 
- **render 함수**의 인자는 3개로 이루어져 있다. 세번째 인자는 딕셔너리이다. 꼭 기억하자.

```python
from django.shortcuts import render    # django -> shortcuts.py -> render라는 함수 import
from .models import Post               # 같은 디렉터리에 있는 models.py에서 Post 클래스 import

def post_list(request):
    qs = Post.objects.all()                               # QuerySet 이라는 변수로 Post 모델의 모든 데이터를 가져오기
    return render(request, 'blog1/post_list.html', {      # 해당 함수가 호출되면 blog1 앱 내부의 post_list.html를 랜더링해주는 것
        'post_list': qs,                                  # render 함수 세번째 인자로 qs 변수가 value인 딕셔너리 추가
    })                                                    # 그래서 html에 해당 목록을 넘겨주는 것
```

- views.py라는 건, 어떤 요청이 왔을 때 호출되는 함수를 의미한다. qs라는 변수를 설정해서 Post 모델의 모든 데이터를 가져오고, render 함수 세번째 인자에 딕셔너리 형태로 넘겨준다.


### template 설정
- blog1이라는 디렉터리 안에 templates라는 디렉터리를 만들자. 그리고 그 안에 blog1이라는 앱과 동일한 이름의 디렉터리를 하나 더 만든다. 또 그 안에는 post_list.html을 만들자.

```html
<h1>Post List</h1>


{% for post in post_list %}
    <h2>{{ post.title }}</h2>
        {{ post.content }}
{% endfor %}
```

- 이 template의 경우, view 함수가 호출되고 render 함수로 해당 html를 사용하게 되었기 때문에 django template language를 이용해 표현할 수 있다.


### app 내부 urls.py 설정
- blog1 앱 내부의 urls.py로 들어가자.
```python
from . import views    # 현재 디렉터리에서 views.py를 import 
from django.urls import path  # django -> urls 패키지에 conf.py 안에 있는 path 함수 import

urlpatterns = [
    path('', views.post_list, name='post_list'),    # 두번째 인자를 함수로 넘긴 것이지, 괄호를 사용해서 호출하면 안된다.
]
```

- 앱 내부에 있는 urls.py에서 views.py을 import 하고, urls 패키지 -> conf.py에 있는 path 함수도 import한다.
- 그리고 path 함수를 사용해서 위의 view 함수를 연결시키자. 


### 웹 브라우저에서 화면 확인
- 웹 브라우저에서 localhost:8000/blog1 으로 들어가보면, 우리가 작성한 post_list.html의 내용이 보여지게 된다.


### **vs code에서 command + P 하게되면 파일 검색 가능**
