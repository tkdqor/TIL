## 기본 View 클래스, 템플릿 View 클래스 사용하기
- 장고에서는 뷰 클래스를 제공한다.
- 기본으로 View 클래스가 있고, 이것을 확장한 TemplateView 라는 클래스를 활용해보자. 
  - **View 클래스**는 장고에서 뷰에 필요한 내용들을 재사용할 수 있게 해주는 기본 클래스이다. 그리고 다른 확장 뷰 클래스들(TemplateView, CreateView) 의 최상위 부모 클래스가 된다. 그래서 get과 post라는 메소드를 지원한다.
  - **TemplateView 클래스**는 View 클래스에서 확장해서(View 클래스를 상속받는 자식 클래스라고 생각하자) Template 렌더링을 최소한의 코드로 작성할 수 있게 해주는 장고 뷰 클래스이다. template_name 이라는 멤버 필드에 템플릿을 지정하면 된다.
    추가적으로 get_context_data라는 메소드를 사용한다. 
    


### 사용해보기
- 먼저, views.py에서 django.views라는 패키지 하위에 View 클래스들이 있다. 그래서 import 해서 가져오면 된다.
```python
from django.shortcuts import render
from django.views import View 
from .models import Task, ChecklistItem

...

class TaskListView(View):
    template_name = 'pages/task_list.html'

    def get(self, request):
        tasks = Task.objects.all()
        context = {
            'tasks': tasks,
        }
        return render(request, self.template_name, context)


    def post(self, request):
        context = {}
        return render(request, self.template_name, context)
```


- 우리가 메인 화면으로 구현할 Tasklist 뷰를 클래스로 선언해보는 것이다. View를 상속받도록 해야한다. 그리고나서 get메소드와 post메소드를 정의해준다.
- View를 상속받아서 뷰 클래스를 정의하고 get메소드를 정의하면 - GET방식으로 접근한 요청에 대해서 처리를 하게 되고, post메소드는 - POST방식으로 접근한 요청을 처리하게끔 약속이 되어있다. 
- 또한, get과 post메소드는 HTTP Request 객체를 받도록 되어있다. 
  - get 메소드에서 우리가 페이지에 접근했을 때, 할 일 목록을 보여주는 것이 맞기에 Task 모델 데이터를 전부 가져와준다.
  - post 메소드도 설정해주면 되는데, 실제로 첫 메인 페이지에서 사용자가 get으로만 접근하고 post로 접근하는 경우가 없기 때문이다. 그래서 일단은 지금은 간단하게 정의해놓기만 하자.
  
- 그리고 get이든 post든 둘 다 렌더링하는 페이지가 같기 때문에 -> 이 부분을 template_name이라는 필드를 만들어서 값을 넣어주고 사용할 수 있다.


- 이제 task_list.html를 생성해주자.
```html
{% extends 'common.html' %}

{% block content %}
<div class="row row-cols-2 row-cols-md-4 g-4 mt-2">
    {% for item in tasks %}
    <div class="col">
        <div class="card text-white bg-primary mb-3">
            <div class="card-header">{{ item.type }}</div>
            <div class="card-body">
                <h5 class="card-title">{{ item.title }}</h5>
                <p class="card-text">
                    <span class="badge bg-light text-dart">
                        {{ item.due|date:'Y년 m월 d일 H시 i분' }}까지
                    </span>
                </p>
            </div>
        </div>
    </div>
    {% endfor %}
</div>
{% endblock %}
```

- 위의 코드에서 {{ item.due }} 이렇게 출력하면 보기 안좋게 출력이 된다. 그래서 뒤에 형식을 붙여주면 좋다. -> {{ item.due|date:'Y년 m월 d일 H시 i분' }} 이러한 형식으로 task의 종료날짜를 포매팅하겠다는 의미이다.
- 관련 공식 문서 : https://docs.djangoproject.com/en/4.0/ref/templates/builtins/


- 위와 같이 설정하고 난 후, 이제는 urls.py로 가보자. 프로젝트 디렉터리 내부에 있는 urls.py를 설정하자.
- **뷰 클래스로 정의한 클래스는 -> as_view라는 메소드가 있다. 그걸 호출해서 처리하게 된다.**

```python
from taskapp.views import TaskListView

...
urlpatterns = [
    # 할 일 목록
    # path('', views.index, name='index'),
    path('', TaskListView.as_view(), name="index"),
    ...
```




- 먼저 해당 뷰 클래스를 import 해야한다. 그리고 as_view 함수를 호출해서 연결한다. 


* * *
- **지금까지 작성해서 서버를 구동시켜보면, DB에 데이터가 없어 출력이 되지 않는다. 그래서 django shell를 이용해서 데이터를 채워보자.**
  - django shell은 우리가 개발한 장고 프로젝트에서 원하는 코드만 실행할 수 있게 해주는 툴이다.

- 서버를 끄고 해도 되지만, terminal 부분에 +를 누르면 터미널에 하나 더 열린다. 이렇게 하면 서버를 끄지 않고 동시에 할 수 있다.

```terminal
python manage.py shell

>>> from taskapp.models import Task
>>> from django.utils import timezone
>>> task = Task.objects.create(title="Test", type=Task.TaskType.JOB, due=timezone.now())
>>> task = Task.objects.create(title="Test1", type=Task.TaskType.JOB, due=timezone.now())
>>> task = Task.objects.create(title="Test2", type=Task.TaskType.JOB, due=timezone.now())
```

- **from django.utils import timezone -> 이것은 models.py에서 DateTimeField를 설정과 관련이 있다. 장고에서는 timezone이라는 장고 내부에 있는 객체를 통해서 date값을 넣어줄 수 있다.**
  - **웹서버가 timezone이 각각 다른곳에서 접속할 수 있다. 한국, 미국 등등 우리가 오전 9시라고 설정했다 하더라도 미국은 오후 6시 이렇게 될 수 있다. 그래서 이러한 부분을 접속하는 location마다 timezone를 구분해서 저장하거나 불러오기 위해 신경써서 개발해야 된다. 근데 장고에서는 이러한 부분을 원할하게 처리하기 위해 timezone이라는 것을 import해서 사용할 수 있게 된다. timezone.now() 이렇게 사용하면 현재 시각이 들어가게 된다.**
  
- Task모델에 데이터를 3개 만들어준다. 그리고나서 컨트롤 + D로 빠져나올 수 있다.

- 그리고나서 브라우저 화면을 보면, 3개의 데이터와 관련해서 카드 형식의 할 일 리스트가 출력되는 것을 확인할 수 있다. 

* * *
### Generic View
- **우리가 자주 작성하는 뷰의 형태들을 최대한 공통화해서 View 클래스를 한 번 더 표준화한 클래스들을 의미한다. 장고에서만 사용되는 용어이며, 그 중 하나인 Template View를 활용해서 지금 구현한 TaskList를 코드를 줄여보는 작업을 해보자.** 다시 views.py로 가보면,

```python
from django.views.generic import TemplateView

...

class TaskListView(TemplateView):
...
```

- **먼저 generic이라는 패키지 내부에 있는 TemplateView를 import 해주자.**
- **그리고 아까 정의했던 TaskListView를 View가 아닌 TemplateView를 상속받는 것으로 수정해보자.**
  - **기본적으로 TemplateView는 GET방식을 위한 클래스이다. 그래서 POST 부분은 필요가 없다.**
  
- **VS Code에서 TemplateView 위쪽에 command를 누르고 클릭을 해보면 -> TemplateView의 원형을 알 수 있다.**

```python
class TemplateView(TemplateResponseMixin, ContextMixin, View):
    """
    Render a template. Pass keyword arguments from the URLconf to the context.
    """
    def get(self, request, *args, **kwargs):
        context = self.get_context_data(**kwargs)
        return self.render_to_response(context)
```

- **해당 View를 보면, View를 상속받고 있다. get메소드는 원래 View 클래스에 있는 메소드인데, TemplateView에서 get메소드에 대해 한 번 더 덮어씌운 것이다. (override 개념) 이러한 개념이 클린코드 관점에서는 좋은 건 아니지만, 장고는 코드를 줄이는 것에 있어 집착하는 경향이 있다고 한다. 그래서 이런식으로 기존의 선언되었던 get메소드를 덮어씌워서 get_context_data라는 함수와 render_to_response라는 2개의 함수를 통해서 get메소드를 사용할 수 있도록 만들어놓았다.**
  - 그래서 우리가 get_context_data라는 함수만 정의를 하면 -> context 데이터를 가지고 와서 템플릿을 렌더링 시켜준다고 이해하면 된다.

- **그리고 TemplateResponseMixin 라는 클래스는 -> 마찬가지로 command하고 눌러보면, template_name이라는 내부 멤버 필드가 정의되어 있다. 이걸 그대로 활용해서 그 안에 정의되어있는 render_to_response 메소드에서 렌더링을 해주는 것이다.** 

```python
class TemplateResponseMixin:
    """A mixin that can be used to render a template."""
    template_name = None
    template_engine = None
    response_class = TemplateResponse
    content_type = None

    def render_to_response(self, context, **response_kwargs):
        """
        Return a response, using the `response_class` for this view, with a
        template rendered with the given context.

        Pass response_kwargs to the constructor of the response class.
        """
        response_kwargs.setdefault('content_type', self.content_type)
        return self.response_class(
            request=self.request,
            template=self.get_template_names(),
            context=context,
            using=self.template_engine,
            **response_kwargs
        )
        ...
```       

- 이러한 내용들은 장고 프레임워크를 개발한 개발자가 작성을 해놓은 것이고 우리는 활용을 하면 된다.
- 그래서 다시 views.py로 오면,

```python
from django.views.generic import TemplateView
...

class TaskListView(TemplateView):
    template_name = 'pages/task_list.html'

    def get_context_data(self, **kwargs):
        tasks = Task.objects.all()
        return {
            'tasks': tasks,
        }
```
    
    
- TemplateView를 상속받고, template_name이라는 이 변수를 -> 우리가 지정하고자 하는 템플릿으로 지정해주면 된다. 그러면 위에서 본 render_to_response 함수가 알아서 꺼내 쓰게 된다.
- 그리고 위에서 본 get_context_data라는 함수를 우리쪽에서 구현하면 되는데, 여기서는 template에 넘겨줄 변수만 정의해서 return으로 던져주면 된다.
  - 그러면 View 클래스를 이용한 것과 동일하게 화면을 확인할 수 있다. 그래서 코드가 전보다 줄일 수 있고, 코드를 줄이는 것에 일조할 수 있다는 점을 확인하자.
  - **그리고 class TaskListView(TemplateView): -> 와 같이 누군가가 작성해놓은 원형이 있고 그 원형을 내가 필요에 맞게 가공해서 쓸 수 있도록 상속을 받으면, 원형이 가지고 있는 여러가지 속성들을(template_name이라든가 get_context_data 함수 등을 우리쪽에서 재정의하고 나머지 로직 흐름은 상속받은 부모 클래스가 가지고 있는 로직을 그대로 사용할 수 있게 함으로써, 재사용성을 높여주게 된다.**
  - **이러한 부분이 객체 지향 언어라고 볼 수 있는 것이다.**
