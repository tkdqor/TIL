## FormView 클래스
- Form View를 활용해서 Task를 생성하는 페이지 만들기 -> **FormView 클래스로 Form 형태로 데이터를 받고 새로운 데이터를 생성할 수 있음**
- **ModelForm -> Form을 지정해서 폼을 알아서 그려주고 DB에 저장할 수 있게 해주는 클래스**
  - 데이터 처리 후 success_url로 지정된 주소로 자동 리디렉션을 해준다. template View랑 동일하게 template_name으로 템플릿 지정이 가능하다.


### 실습해보기
- **먼저 앱 디렉터리 내부에 forms.py를 생성하기**
- 그 다음, django 하위에 forms를 import / Task 모델도 import 해주기
- 그리고나서 모델 폼을 클래스로 생성

```python
from django import forms
from .models import Task

class TaskForm(forms.ModelForm):
    class Meta:
        model = Task
        fields = ['title', 'type', 'due']
```        

- 모델 폼 클래스 내부에는 Meta 클래스를 설정해서 어떤 모델인지 설정하고 어떤 필드를 form에서 받도록 할 것인지 설정해준다.

* * *
- **그리고 이제 View에서 해당 form View를 이용해서 코드를 구현해보자. views.py로 돌아가서 설정하기.**
```python
from .forms import TaskForm
from django.views.generic import TemplateView, FormView

...

class TaskCreateView(FormView):
    template_name = 'pages/task_create.html'
    form_class = TaskForm
    success_url = '/'
    
    def form_valid(self, form):
        form.save()
        return super().form_valid(form)
```

- 맨 처음으로 forms.py의 TaskForm을 import 해주는 게 시작. 그리고 추가로 FormView라는 것도 import 해주기.
- 그리고 새로운 View를 설정해서 FormView를 상속받는 것으로 설정하고 template_name을 설정한다. form_class라는 변수에는 우리가 앞에서 정의한 TaskForm을 입력해준다. 마지막으로 success_url에는 
  생성이 되고나서 어떤 url로 전달될 것인지 설정한다. '/'는 메인페이지로 보내는 것이다.
  - 또한, 해당 클래스에서 추가로 메소드를 설정해야 한다. form_valid라는 메소드이다.
  
- **TaskForm처럼 모델 Form의 특징은, 알아서 request를 읽어서 파싱을 해준다. form의 필드들에 request 데이터들을 집어넣어준 다음에, 우리가 form_valid 메소드를 통해 save만 호출해주면 -> 실제 DB로 
  commit 까지 이루어지게 된다.** 
  - form_valid라는 메소드는 기본적으로 데이터가 request되서 전달이 되면 form이라는 객체로 빚어진 다음에 form데이터의 valid 체크를 진행하게 된다. 그 체크가 다 끝나게 되면 이 form 데이터가 
    유효하다 라는 게 검증이 된 이후에 사용할 수 있는 로직들을 form_valid 메소드 아래에 정의를 하는 것이다. 지금은 바로 save()를 호출해서 DB에 저장하겠다고 한 것이다.
  - 기존 form_valid의 로직은, command 누르면서 form_valid를 클릭해보면, 

```python
class FormMixin(ContextMixin):
    """Provide a way to show and handle a form in a request."""
    initial = {}
    form_class = None
    success_url = None
    prefix = None
    
    ...
    
    def form_valid(self, form):
        """If the form is valid, redirect to the supplied URL."""
        return HttpResponseRedirect(self.get_success_url())       
```

- 위와 같이 그냥 sucess_url로 리디렉션 시켜주는 게 전부이다. 그래서 우리가 위의 def form_valid(self, form): 를 재사용하기 위해서 -> return super().form_valid(form) 이런식으로 return을
  해줄 때 super()를 붙여서 form_valid를 그대로 호출하겠다고 코드를 작성한 것이다.
  - **super()도 command로 눌러서 확인해보자. super()는 기본적으로 지금 상황에서는 FormView와 같이 상위 객체, 부모 클래스에 있는 정의된 필드나 메소드를 그대로 접근할 때 사용한다. FormView를 
    command로 클릭해서 들어가고, BaseFormView를 클릭하고 또 FormMixin를 클릭하면, form_valid라는 메소드가 정의되어 있는 것을 확인할 수 있다.**
  - 즉, super()는 부모 클래스가 있을 때, 먼저 부모 클래스를 탐색하고 만약 부모 클래스 내부에 찾고자 하는 함수가 정의되어 있지 않다면 할아버지 클래스로 가서 찾게 된다. 할아버지도 없으면 증조까지 가서 계속 
    탐색해서 찾고하 하는 함수가 등장할 때 까지 올라간 다음에 그 함수를 호출하게 되는 것이다. 만약, 뿌리까지 올라가서도 해당 함수가 없다면 오류가 나게 된다. 

* * *
- **이제 task_create.html이라는 파일을 만들어보자. 앱 내부 templates - pages 디렉터리 내부에 생성한다.**
```html
{% extends 'common.html' %}

{% block content %}
<form method="POST" action="{% url 'create-task' %}">
    <div class="row">
        <div class="col-12">
            {% csrf_token %}
            {{ form }}
            <button type="submit">추가하기</button>
        </div>
    </div>
</form>
{% endblock %}
```

- 이렇게 form element를 사용해서 POST방식으로 설정해주자. 안에 django template langauge로 {{ form }} 이렇게만 설정해주면 된다..?
- 그리고 common.html에서 추가하기 버튼을 생성한다.
```html
<nav>
  <!-- 추가하기 버튼 -->
                <ul class="nav justify-content-end">
                    <li class="nav-item"> 
                        <a class="nav-link" href="{% url 'create-task' %}">
                            <button class="btn btn-dark">
                                할 일 추가
                            </button>
                        </a>
                    </li>
                </ul>
</nav>
```

- 위와 같이 입력한 다음, 설정해준 create-task라는 이름의 url를 생성해야 한다. 프로젝트 내부 디렉터리에 urls.py로 가서 추가하자.
```python
from taskapp.views import TaskListView, TaskCreateView

...

urlpatterns = [
    # task 생성
    path('task/', TaskCreateView.as_view(), name='create-task'),
    ...
]
```


- 먼저 맨 위에 Views.py에 우리가 정의한 TaskCreateView를 import 해준다. 그 다음, 할 일 생성 path() 함수에서 설정한다. as_view()함수로 호출해준다.
- 그러면 이제 브라우저 화면에서 할 일 추가 버튼을 확인할 수 있다. 이 버튼을 클릭하면, 
<img width="1331" alt="image" src="https://user-images.githubusercontent.com/95380638/156488483-83be02c5-0308-435f-96d2-4675d52fb50d.png">

- **이렇게 form 형태를 확인할 수 있게 된다. 하지만, Due 부분은 입력받기 힘들게 되어있기 때문에 이럴때는 -> 자바스크립트 라이브러리를 찾아서 DateTimePicker를 통해 입력받도록 해보자.**
  - 또한, 부트스트랩을 사용해서 모양을 정리해보자. **이럴 때를 위해서 python에는 Crispy forms라는 django 기반의 라이브러리가 있는데, 이걸 설치해서 사용해보자. 이 모듈은 부트스트랩 5를 지원하지 않기 때문에 부트스트랩을 4버전 기준으로 바꿔줘야 한다.**

* * *
### crispy forms -> django 기반 라이브러리

```terminal
pip install django-crispy-forms
...
Successfully installed django-crispy-forms-1.14.0
```

- 프로젝트 내부 디렉터리에 있는 상황에서 pip를 이용해 crispy forms를 다운해보자.
  - 그리고 django에서 인식할 수 있게 프로젝트 디렉터리 내부 settings.py로 가서 
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'crispy_forms',
    'taskapp',
]

CRISPY_TEMPLATE_PACK = 'bootstrap4'
```

- 이렇게 추가해주자. 그리고 바로 아래쪽에다가 crispy_forms에서 지원하는 세팅 변수를 추가해야 한다. 이 변수의 값을 bootstrap4로 설정한다.

- 이제 다시 task_create.html로 돌아가자.
```html
{% extends 'common.html' %}
{% load crispy_forms_tags %}

{% block content %}
<form method="POST" action="{% url 'create-task' %}">
    <div class="row">
        <div class="col-12">
            {% csrf_token %}
            {{ form|crispy }} 
            <button type="submit">추가하기</button>
        </div>
    </div> 
</form>
{% endblock %}
```

- {% load crispy_forms_tags %} -> cirspy forms라는 모듈에서 가지고 있는 태그들을 load한다는 의미이다.
  - **{{ form|crispy }} -> 이렇게만 봤을 때, | 이 기호를 쓰고 뒤에 태그명을 써주면 되는데 여기서 crispy는 사실 함수이다. 그래서 왼쪽에 있는 것을 value로 받는 함수를 구현한 것이다. 즉, template에서는 | 이 기호를 사용해서 이어서 태그명을 넣어주면 해당 함수가 실행된다.**
<img width="1202" alt="image" src="https://user-images.githubusercontent.com/95380638/156490797-b3b3d90a-46dc-4017-8350-c4f8c999d7a0.png">

- 그러면 이렇게 form이 달라지게 된다.
- **그리고 이제는 Due 부분에 자바스크립트로 DateTimePicker가 뜨도록 할 것이다. Flatpickr라는 라이브러리인데, 일단 CDN으로 자바스크립트와 CSS를 제공하고 있으니까 쉽게 가져올 수는 있다. 그래서 common.html에서 설정해보자.**

* * *
### DateTimePicker 설정 -> Flatpickr 라이브러리 사용

```html
<head> 
  ...
  <!-- Flatpickr 라이브러리 CSS 연결 -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
  ...
  <!-- Flatpickr 라이브러리 Javascript 연결 -->
  <script src="https://cdn.jsdelivr.net/npm/flatpickr"></script>
</head> 
```

- 해당 사이트를 참고해서 CDN 가져오기. https://flatpickr.js.org/getting-started/
- 이렇게 설정하고, 로딩이 되는지 보려면 -> 브라우저 개발자도구를 키고, 새로고침 한 다음 네트워크를 보면 flatpickr 라는 이름이 잘 뜨는 것을 확인할 수 있다.



- DateTimePicker가 그려질 페이지에서 웹 페이지가 로딩될 때 불려지도록 코드를 작성해보자.
- task_create.html로 가서 자바스크립트를 작성해야 한다.
```html
<form>
  ...
</form>

<script>
    window.onload = function() {
        flatpickr('input[name="due"]', {enableTime: true});
    }
</script>
```

- **window는 : 브라우저 객체 모델(BOM, Browser Object Model)로 자바스크립트의 모든 객체, 전역 함수, 전역 변수 등을 의미한다.**
  - **window.onload는 : 자바스크립트가 문서가 모두 준비된 상황 이후에 발동 및 호출하도록 하는 코드이다.**

- 이벤트가 발생할 때, 어떻게 할지 자바스크립트로 작성. 사용법은 flatpickr 공식 홈페이지를 참고. https://flatpickr.js.org/
  - window가 onload 이벤트가 발생할 때 -> flatpickr('input[name="due"]', {enableTime: true}); 이 함수가 실행되도록 설정. 
<img width="400" alt="image" src="https://user-images.githubusercontent.com/95380638/156493685-55878d39-a0bc-4041-a29a-80a628da2e5e.png">

- 이렇게 하면 다음과 같이 달력이 뜨면서 날짜를 클릭할 수 있게 된다.

- 여기까지 하고 나서, 이제 잘 저장이 되는지 확인해보기.
  - 브라우저에서 할 일 추가 버튼을 누르고, Title / Type / Due를 선택한 다음 추가하기 버튼을 클릭하면 실제로 메인 페이지에서 할 일이 하나 뜨게 된다.
