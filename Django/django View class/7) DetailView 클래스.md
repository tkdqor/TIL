## DetailView 클래스
- DetailView는 하나의 데이터를 조회하고 보여주는 클래스이다. 클래스 내부에 pk_url_kwarg 필드를 설정해서 상세 페이지를 만들 수 있다.


### 실습해보기
- 먼저 views.py에서 DetailView를 import 하는 것으로 시작.
```python
from django.views.generic import TemplateView, FormView, CreateView, ListView, DetailView

...
# DetailView를 상속받은 클래스
class TaskDetailView(DetailView):
    model = Task
    template_name = 'pages/task_detail.html'
    pk_url_kwarg = 'task_id'


```


- 그리고 DetailView를 상속받는 새로운 View를 설정하기. DetailView도 다른 generic View와 비슷하게 model과 template_name 멤버에 각각 연결한 값들을 선언하는 것 부터 시작한다.
  - model은 DetailView에서 보여질 모델을 설정.
- 또한, 추가로 **pk_url_kwarg 멤버 변수를 선언**해줘야 한다.
  - **해당 변수는 request 데이터로 넘어오는 primary Key 역할을 할 parameter 변수이름을 지정해주는 역할을 한다. 우리는 urls.py에서 task_id로 Primary Key가 전달되도록 처리할 것이기 때문에 task_id로 
    설정하면 된다.**
    
    
- 이어서 urls.py에서도 설정하자.
```python
urlpatterns = [
from taskapp.views import TaskListView, TaskCreateView, TaskPreviousListView, TaskDetailView

    ...
    # task 1개 조회
    path('task/<int:task_id>/', TaskDetailView.as_view(), name='view-task'),
    
``` 
 
- 먼저, 우리가 View에서 설정한 TaskDetailView를 import 해주고 path() 함수에서 as_view() 메소드를 사용해 호출해준다.
- 그리고 path parameter로 task_id가 선언되어 있는데 이 task_id가 위에 view에서 pk_url_kwarg이라고 생각하면 된다.
- 여기까지만 설정을 해도 task_id로 전달된 task를 조회해서 출력하는 View code는 이걸로 끝이다.
  - 하지만 추가로 우리는 연결된 체크리스트도 출력해야 되기 때문에, DetailView 클래스 내부에 context 데이터에 체크리스트를 담아서 같이 넘겨줘야 한다. 다른 generic View처럼 get_context_data 메소드를 
    구현해서 기존 클래스의 메소드를 호출하고, 그 context에 체크리스트를 포함시켜서 return 해주면 된다.
    - get_context_data가 호출되는 시점에서는 self.object로 조회된 레코드 데이터가 들어가 있기 때문에 task의 조건으로 넘겨주면 된다.

* * *
```python
...
# DetailView를 상속받은 클래스
class TaskDetailView(DetailView):
    model = Task
    template_name = 'pages/task_detail.html'
    pk_url_kwarg = 'task_id'

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['checklists'] = ChecklistItem.objects.filter(task=self.object).all()
        return context
```

- 이렇게 DetailView 내부에 설정을 해준다.
- 우리는 get_context_data 메소드를 그대로 사용할 수 있고, super().get_context_data()로 기존 부모 클래스의 함수를 호출해서 기존 context를 일단 받아온다. 그리고 context에다가 checklists를 
  넣어주는 것이다.
  - checklists는 ChecklistItem이라는 모델에 접근하여 해당 task로 1:N관계에 있는 데이터만 가져오도록 필터링을 해준다.

- **get_context_data가 호출되는 시점에서는, self.object라는 것이 생긴다. 현재 Task라는 모델, 즉 DetailView에서 랜더링되어야 할, path parameter로 받은 pk값을 파싱해서 알아서 데이터가 조회된 상태로 self.object에 들어가 있는 것이다.**
  - 그래서 이 self.object를 ChecklistItem 모델의 task column으로 필터링해주면 해당 task만 가져오라는 조건이 성립되는 것이다.


- 이제는, template 파일을 설정해보자. templates - pages 디렉터리 안에 task_detail.html를 생성한다.
```html
{% extends 'common.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-12">
        <h4 class="mt-2 p-2">
            <span class="badge badge-info">
                {{ object.get_type_display }}
            </span>
            {{ object.title }}
        </h4>

    </div>
</div>

{% endblock %}
```

- **task_detail template에서는 우리가 상세 내용을 출력할 때, View에서 설정했었던 self.object가 template에서는 -> object라는 이름의 변수로 활용할 수 있게 된다.** 지금은 일단 {{ object.get_type_display }} 이렇게 적어보자. 즉, object가 Task 모델에 인스턴스이기 때문에 {{ object.title }} 이렇게도 출력이 가능하다.

- 그래서 지금까지의 코드를 설정하고 브라우저에 http://localhost:8000/task/1/ 이렇게 입력해보면, Task모델의 pk가 1번인 데이터를 출력해준다.






