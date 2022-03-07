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
        <dl class="row mt-4 p-2">
            <dt class="col-sm-2">마감일</dt>
            <dd class="col-sm-10">{{ object.due|date:'Y년 m월 d일 H시 i분' }}</dd>
            <dt class="col-sm-2">생성일</dt>
            <dd class="col-sm-10">{{ object.created_at|date:'Y년 m월 d일 H시 i분' }}</dd>
        </dl>
    </div>
</div>

{% endblock %}
```

- **task_detail template에서는 우리가 상세 내용을 출력할 때, View에서 설정했었던 self.object가 template에서는 -> object라는 이름의 변수로 활용할 수 있게 된다.** 지금은 일단 {{ object.get_type_display }} 이렇게 적어보자. 즉, object가 Task 모델에 인스턴스이기 때문에 {{ object.title }} 이렇게도 출력이 가능하다.

- 그래서 지금까지의 코드를 설정하고 브라우저에 http://localhost:8000/task/1/ 이렇게 입력해보면, Task모델의 pk가 1번인 데이터를 출력해준다. 그리고 해당 task의 마감일과 생성일이 출력된다.

* * *
- 이제, checklist를 출력해보자. table를 만들어서 출력해보기. 밑에 코드는 위의 task_detail.html에 이어서 입력하는 코드이다.
```html
...
<!-- 체크리스트 출력 부분 -->
<table class="table">
    <tbody>
    {% if checklists %}
    {% for check in checklists %}
        <tr>
            <td style="width:20px;">
                {% if check.checked %}
                <button class="btn btn-link">
                    <i class="bi bi-check-square"></i>
                </button>
                {% else %}
                <button class="btn btn-link">
                    <i class="bi bi-square"></i>
                </button>
                {% endif %}
            </td>
        </tr>
    {% endfor %}
    {% else %}
        <tr><td>체크리스트가 없습니다.</td></tr>
    {% endif %}
    </tbody>
</table>
```

- **일단 우리가 View에서 checklists라는 변수를 설정해서 template에 넣어준다. 만약 할 일에 checklists가 없다면 체크리스트가 없다고 뜨게끔 설정했다. 만약 있으면 for문을 실행해서 checklists를 하나씩 꺼내게 된다.**
  - 하나씩 꺼낼 때, checklists의 항목 하나가 체크가 되어있는지 아닌지에 따라서 출력이 다르게 나와야 하기 때문에 -> **{% if check.checked %}** 이러한 코드를 입력해준다. 20px에 공간에 버튼을 넣는데 이 checked가 True이면 bi-check-square라는 class를 가진 아이콘, 즉 체크가 되어있는 박스를 출력하고 / 그렇지 않을 경우에는 bi-square라는 class를 가진 아이콘을 출력하게끔 설정했다. 체크가 안되어있는 경우이고 체크가 안된 박스를 출력한다.

- 그리고 table의 row를 하나 더 만들어서 **{{ check.content }}** -> 이렇게 체크리스트의 내용물을 출력하도록 한다.

- 또한, 추가로 부트스트랩의 아이콘을 사용하기 위해서 common.html에 head element에 CDN를 추가하자.
```html
<head>
    <!-- bootstrap 아이콘 연결 -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.5.0/font/bootstrap-icons.css">
    ...
</head>
```

- **이렇게 설정해서 브라우저를 확인해보면, 아직 데이터가 없어 버튼을 확인할 수 없다. 그래도 위의 icon이 잘 연결되었는지 확인하려면 -> 브라우저에서 개발자도구를 키고 Network를 선택 - CSS 선택하고 새로고침을 해보면, Name에 bootstrap-icons.css가 뜨는 것을 확인할 수 있다.**

* * *
- 마지막으로, task_list.html에서 링크를 달아 task_detail.html로 연결할 수 있게 해보자. 각 할 일 카드마다 상세보기 링크를 넣어주는 것이다.
```html
{% extends "common.html" %}
{% load pagination_tags %}

{% block content %}
<div class="row row-cols-2 row-cols-md-4 g-4 mt-2">
    {% for item in paging %}
    <div class="col">
        <div class="card text-white bg-primary mb-3">
            <div class="card-header">{{ item.type }}</div>
            <div class="card-body">
                <h5 class="card-title">{{ item.title }}</h5>
                <p class="card-text">
                    <span class="badge bg-light text-dark">
                        {{ item.due|date:'Y년 m월 d일 H시 i분' }}까지
                    </span> 
                </p>
            </div>

            <!-- 상세보기 링크  -->
            <div class="card-footer">
                <a href="{% url 'view-task' item.id %}">
                    <button class="btn btn-dark float-right">보기</button>
                </a>
            </div>
        </div>
    </div>
    {% endfor %}
</div>

...
```

- 이렇게 상세보기 링크를 만들어준다. view-task라는 이름의 url이 상세페이지를 요청하는 path name이니까 설정해준다.


