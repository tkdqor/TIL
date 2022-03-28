## Django의 기본 로직: URLs and Views (1)


### django 프로젝트 구조
- 현재 우리는 first-django라는 프로젝트 디렉터리가 있어서 루트 디렉터리가 된다. 그래서 이 안에 manage.py와 db.sqlite3가 들어가 있고 config디렉터리와 venv가상환경 디렉터리가 있다.
- manage.py는 우리가 django한테 명령어를 쓸 수 있도록하는 python 파일이다.
- config는 설정파일들이 안에 있다고해서 config라는 이름을 지정했다.
  - **이렇게 config나 venv처럼 한 번 설정한 이름은 내가 임의로 수정하면 안 된다.** 즉, 명령어로 만든 폴더나 파일들은 내가 임의로 건들면 안 된다.

- **config안에 있는 python 파일들을 확인해보자.**
  - 던더init던더파일은 해당 디렉터리가 python package라고 선언해주는 약속과 같다. 그래서 이 파일이 들어가 있으면, 이 파일이 들어가 있는 폴더는 python package라고 약속되어있다. 안에는 아무것도 없다.
  - asgi.py와 wsgi.py는 배포와 관련이 있다.
  - settings.py는 정말 중요하다. django가 실행되는데 있어서 필요한 설정 변수, 값들을 다 확인할 수 있다.
  - urls.py는 url pattern를 관리하는 파일이다.


### App 추가하기
- **항상 명령어는 가상환경이 활성화 되어있고 + ls했을 때 manage.py가 있는 위치에서 해야한다.**
- django 프로젝트는 django App들로 구성이 되어있다. 이번에는 계산기를 위한 App를 만들어보자.

```terminal
django-admin startapp demos
```

- **이렇게 입력하면 루트 디렉터리 내부에 demos라는 새로운 디렉터리가 생성된다.**
  - 해당 App 내부에 migrations 폴더는 데이터베이스 기록을 남길 때 사용하는 폴더이다.
  - admin.py는 django가 제공하는 관리자 인터페이스를 설정할 수 있는 파일이다.
  - apps.py는 해당 App의 이름이나 설정값들을 넣는 파일이다.
  - models.py는 우리가 데이터베이스를 정의할 때 사용.
  - tests.py는 test 코드를 짤 수 있는 곳이다. 
  - views.py는 url pattern을 처리하고 로직을 작성하는 파일이다.

- **그리고 App를 만들면 무조건 --> settings.py에 추가해줘야 한다.** 여기서 INSTALLED_APPS 부분에 우리가 만든 App이름을 추가해준다. 이 때 꼭 뒤에다가 콤마를 붙여주자.

* * *
- **계산기 기능을 만들기 위해, 먼저 demos 안에있는 views.py를 작성해보자.**

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def calculator(request):
    return HttpResponse('계산기 기능 구현 시작입니다.')
```

- 프레임워크는 약속된 게 많은데, 이렇게 요청을 처리하고 기능을 수행하는 함수를 만들때는 --> request라는 인자를 꼭 넣어줘야 한다.
  - 그리고나서 model이나 template 사용없이 텍스트만 응답하기 위해서는 HttpResponse를 입력해야 한다. 그러기 위해서는 from django.http import HttpResponse 이렇게 이미 django안에 만들어져 있는 기능을 가져오는 것이다.

- **이제는 메인 urls.py를 설정하자. django 프로젝트 폴더에 있는 urls.py이다.**

```python
from django.contrib import admin
from django.urls import path
from demos.views import calculator

urlpatterns = [
    path('admin/', admin.site.urls),
    path('calculator/', calculator),
]
```

- 일단 calculator/라는 path를 설정하고 demos라는 App에 있는 views.py에 있는 calculator 함수를 import 해준다.
- 그 다음에 서버를 활성화시키고 calculator/로 들어가면 우리가 설정한 response가 보이게 된다.
  - **이렇게 된 과정은, 지금 브라우저에서 url로 접속해서 -> urls.py에 등록된 url pattern을 찾아서 -> 연결된 view한테 요청을 넘겨준 것이다.**
  - **그리고 View는 지금 model이나 template를 하나도 사용하지 않았다. 그래도 View에서 요청을 받아서 자기가 혼자 응답을 해준 상황이다.**

* * *
- 그리고 이제는 template를 추가해보자. **우리가 만든 demos라는 디렉터리안에 templates라는 이름의 폴더를 만든다. --> 이것도 약속된 것이니까 폴더명이 변하면 안 된다.**
  - 그래서 이 templates 안에서 html를 응답해줄 수 있다. templates 내부에 calculator.html를 만든다. 그리고 간단하게 html element를 구성해놓는다.

- **우리가 위에서 View에서 template를 가져다가 사용한다고 했는데, template는 당하는 파일이다. View에다가 어떤 template를 쓸지 알려줘야 한다.**
  - 다시 View에서 html로 응답해주는 것으로 바꾸는 것이다.

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def calculator(request):
    return render(request, 'calculator.html')
```

- 이런식으로 render라는 함수를 사용해서 request를 인자로 넣어주고 응답하고자 하는 html의 이름을 적어준다. 이렇게 설정하고 브라우저를 새로고침하면 html의 코드가 브라우저에 보여진다.
- **이제 다시 흐름을 보면, 브라우저가 요청을 날렸을 때 --> urls.py에서 받고 --> Views.py에서 template를 가져다가 응답을 해준 것이다.**
  - model은 데이터를 저장하거나 저장된 데이터를 가져올 때 사용한다. 지금은 사용하지 않았다.
