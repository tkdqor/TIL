## DeleteView 클래스
- DeleteView는 기존 데이터를 삭제해주는 View이다. 삭제 처리 후 자동으로 리디렉션 처리를 해준다. 그리고 GET으로 접근하면 삭제 여부를 재확인하는 폼을 렌더링해준다. View에서 보면,

```python
from django.views.generic import TemplateView, FormView, CreateView, ListView, DetailView, UpdateView, DeleteView
from django.urls import reverse

...
# DeleteView 상속받아서 클래스 생성
class ChecklistDeleteView(DeleteView):
    model = ChecklistItem
    pk_url_kwarg = 'check_id'
    template_name = 'pages/checklist_delete.html'
    success_url = '/task/'
    
    def get_success_url(self):
        return reverse('view-task', kwargs={'task_id': str(self.kwargs['task_id'])})
```        



- 먼저 DeleteView를 import해주기. 그리고 모델과 템플릿 이름 등을 설정해준다. 또한, 해당 클래스 내부에 get_success_url 메소드를 설정한다. reverse를 사용하기 위해 import 해주기.
  - DeleteView도 template를 통해서 Form를 그려주는 게 필요하다. 그래서 template_name를 넣었는데, 다만 기존 model Form과는 다르게 삭제 여부를 재확인하는 Form를 생성한다는 점이 다르다.
  - 지금까지는 Checklist를 삭제하는 View 이었다. 이제는 Task를 삭제하는 View를 만들어보자.
  

```
# DeleteView 상속받아서 Task 삭제 클래스 생성
class TaskDeleteView(DeleteView):
    model = Task
    pk_url_kwarg = 'task_id'
    template_name = 'pages/task_delete.html'
    success_url = '/'
```    
    

- 위의 코드처럼 TaskDeleteView를 생성하고 success_url은 메인페이지로 가니까 /만 적어주기. 


- 그리고 이어서 template 2개를 만들어주자. 먼저 checklist_delete.html를 만든다.
```html
{% extends 'common.html' %}

{% block content %}
<form method="POST">
    <div class="row">
        <div class="col-12">
            {% csrf_token %}
            <p>체크리스트 "{{ object.content }}"를 삭제하시겠습니까?</p>
            <button type="submit" class="btn btn-danger float-right">삭제</button>
        </div>
    </div>
</form>
{% endblock %}

```

- 이렇게 template을 작성해보자. 그 다음, task_delete.html도 생성해서 동일하게 입력하기.
```html
{% extends 'common.html' %}

{% block content %}
<form method="POST">
    <div class="row">
        <div class="col-12">
            {% csrf_token %}
            <p>할 일 "{{ object.title }}"를 삭제하시겠습니까?</p>
            <button type="submit" class="btn btn-danger float-right">삭제</button>
        </div>
    </div>
</form>
{% endblock %}
```

- 그 다음에는 url 설정이다. taskproject 디렉터리 내부의 urls.py를 수정하자.

```python
from taskapp.views import TaskListView, TaskCreateView, TaskPreviousListView, TaskDetailView, \
    ChecklistCreateView, ChecklistUpdateView, ChecklistDeleteView, TaskDeleteView
    
urlpatterns = [
    ...
    # task 삭제
    path('task/<int:task_id>/delete/', TaskDeleteView.as_view(), name='delete-task'),
    # task 체크리스트 1개 삭제
    path('task/<int:task_id>/item/<int:check_id>/delete/', ChecklistDeleteView.as_view(), name='delete-item'),
]
```
    
- 먼저 ChecklistDeleteView, TaskDeleteView를 import 해주기. 그리고 url 2개 추가.
- 그리고 이제 상세페이지에서 삭제 버튼을 만들어주면 된다. task_detail.html에서 생성하자.

```html
<!-- 체크리스트 출력 부분 -->
<table class="table">
    <tbody>
    {% if checklists %}              <!-- 하나의 task안에 checklists가 있다면 -->
    {% for check in checklists %}    <!-- checklists 안에 있는 check를 하나씩 빼서 진행 -->
        <tr>
            <td style="width:20px;">
                {% if check.checked %}       <!-- 만약 하나의 체크리스트가 체크가 되서 True라면 체크된 박스 출력 아니면 그냥 박스 출력 -->
                <a href="{% url 'check-item' object.id check.id %}">
                    <button class="btn btn-link">
                        <i class="bi bi-check-square"></i>
                    </button>
                </a>
                {% else %}
                <a href="{% url 'check-item' object.id check.id %}">
                    <button class="btn btn-link">
                        <i class="bi bi-square"></i>
                    </button>
                </a>
                {% endif %}
            </td>

            <td class="align-middle">
                {{ check.content }}
            </td>

            <!-- checklist 삭제 버튼 -->
            <td style="width: 20px">
                <a href="{% url 'delete-item' object.id check.id %}">
                    <button class="btn btn-link">
                        <i class="bi bi-x-square"></i>
                    </button>
                </a>
            </td>
        </tr>
    {% endfor %}
    {% else %}
        <tr><td>체크리스트가 없습니다.</td></tr>
    {% endif %}
    </tbody>
</table>

```

- 이렇게 checklist 삭제 버튼을 생성해주자. 


- 그리고 할 일 삭제 버튼은 할 일 목록페이지인 task_list.html로 가서 추가해주자.
```html
{% extends "common.html" %}
{% load pagination_tags %}

{% block content %}
<div class="row row-cols-2 row-cols-md-4 g-4 mt-2">
    {% for item in paging %}
    <div class="col">
        <div class="card text-white bg-primary mb-3">
            <div class="card-header">
                {{ item.type }}
                <!-- 할 일 삭제 버튼 -->
                <a href="{% url 'delete-task' item.id %}">
                    <button class="btn btn-outline-dark float-right btn-sm">
                        <i class="bi bi-x-lg"></i>
                    </button>
                </a>
            </div>
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

- 이렇게 할 일 삭제 버튼을 생성해준다.

* * *
- 여기까지 코드를 진행해주고 브라우저를 보면, 할 일 목록에서 X 삭제 버튼이 생겼다. 그래서 실제로 버튼을 누르면 삭제하겠냐고 물어보고, 삭제 버튼을 누르면 삭제가 된다.
- 그리고 1개의 할 일의 보기를 클릭해서 보면, 1개의 체크리스트마다 삭제 버튼이 생겼다. 이것도 역시 버튼을 누르면 물어보고 삭제가 된다.

