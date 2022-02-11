## Template
- 웹페이지의 레이아웃을 django에서 Template이라고 부른다. 기본적으로 HTML 코드이고 동시에 원하는 부분에 데이터를 넣어 사용할 수도 있다.
- django template language : python 코드는 아니지만 변수를 활용할 수 있고 사용방식이 유사하다.


## 실습
- 먼저 posts 앱 내부에다가 templates 라는 디렉터리 생성
  - 그리고 그 안에는 앱 이름과 동일한 posts 디렉터리를 추가로 생성. 이 posts 디렉터리 내부에 index.html 생성하기.

- 또한, view index 함수 수정해주기
```python
from django.shortcuts import render

def index(request):
    return render(request, 'posts/index.html')
```    
    
- render 함수를 사용하면 django template를 사용할 수 있게 된다. 첫번째 인자로 HTTP Request 정보인 request를 넣어주고, 두번째 인자는 우리가 사용하고자 하는 template의 경로를 적어주자. 즉, 우리가 지정한 경로에 있는 template를 사용해서 HTTP Response로 만들어주는 역할을 해준다.
  - 추가로, settings.py에 INSTALLED_APPS에 posts 앱을 추가해주자.

- 이러한 설정을 하고 localhost:8000/posts을 입력하면 index.html이 실행되는 것을 확인할 수 있다.


### Template namespacing
- django에서는 일반적으로 app마다 templates 라는 디렉터리를 만들어서 레이아웃을, 정확하게는 django template를 app 단위로 쪼개서 관리한다. 만약 view 함수에서 render를
```python

def index(request):
    return render(request, 'index.html')
```

- 이런식으로 설정했다면, App마다 templates 폴더 내부에 index.html을 찾게 된다. 그래서 django는 이 경우에 app과 상관없이 프로젝트 내부에 등록된 모든 app의 template 디렉터리 내부를 검색하게 된다. 그래서 우리가 의도하지 않은 다른 HTML 파일을 불러올 수도 있다.

```python
def index(request):
    return render(request, 'posts/index.html')
```

- 그래서, render 함수의 경로를 위와 같이 수정해주면 -> 똑같이 모든 app의 template 내부를 검색하지만 지금은 template 디렉터리 내부에 posts 디렉터리 내부에 index.html이 있어야 불러오게 된다. 따라서 이렇게 설정하면 다른 앱의 HTML 파일을 불러오지 않게 된다.
- 그리고 django는 등록된 모든 app를 대상으로 검색하게 되는데, 이 때는 settings.py에 등록을 했는지 여부로 판단하게 된다.

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'posts',
]
```
- 위의 코드처럼 그냥 app의 이름만 입력해서 등록할 수도 있고,

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'posts.apps.PostsConfig',
]
```

- 이렇게도 등록이 가능하다. 이 경로는 posts 패키지 내부에 -> apps.py라는 모듈이 자동생성 되어있고 -> 그 모듈안에는 PostsConfig라는 클래스가 있다. 이 클래스를 등록해주는 것이라고 생각하면 된다. 
