## Form View 클래스
- Form View를 활용해서 Task를 생성하는 페이지 만들기
- ModelForm, Form을 지정해서 폼을 알아서 그려주는 클래스
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

- 그리고 이제 View에서 해당 form View를 이용해서 코드를 구현해보자. views.py로 돌아가서 설정하기.
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

- 이제 task_create.html이라는 파일을 만들어보자. 앱 내부 templates - pages 디렉터리 내부에 생성한다.
```html
{% extends 'common.html' %}

{% block content %}
<form method="POST" action="">
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

- 위와 
