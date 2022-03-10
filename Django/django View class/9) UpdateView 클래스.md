## UpdateView 클래스
- 이제 체크 처리를 위한 페이지를 구현해보자. 
- UpdateView는 기존 데이터를 조회한 다음, 업데이트 처리를 해주는 View이다. 그리고 ModelForm의 기능도 내장하고 있어 폼 렌더링까지 지원한다. 또한, CreateView와 사용방법도 비슷하다.

- View 에서 수정해보기.

```python
from django.views.generic import TemplateView, FormView, CreateView, ListView, DetailView, UpdateView

...
class ChecklistUpdateView(UpdateView):
    model = ChecklistItem
    fields = ['checked']
    template_name = 'pages/checklist_update.html'
    success_url = '/task/'
    pk_url_kwarg = 'check_id'
    
    def get_success_url(self):
        return self.success_url + str(self.kwargs['task_id']) + '/'
```

- 먼저, UpdateView import 진행. 그리고 UpdateView를 상속받아 새로운 클래스를 정의하기. 
  - 해당 클래스에 모델과 템플릿 이름을 설정하고, pk_url_kwarg은 현재 클릭한 체크리스트 아이템 pk의 path parameter 명이 된다.
  - 그 아래쪽에는 get_success_url를 override 해서 작성.

- 그리고 이제 template를 작성해보자. checklist_update.html를 만든다.
- 해당 template은 checklist_create.html 내용을 복사해서 가져오자.

```html
{% extends 'common.html' %}
{% load crispy_forms_tags %}


{% block content %}
<form method="POST">
    <div class="row">
        <div class="col-12">
            {% csrf_token %}
            {{ form|crispy }} 
            <button type="submit" class="btn btn-primary float-right">수정하기</button>
        </div>
    </div> 
</form>

{% endblock %}
```

- 추가하기 라는 멘트를 수정하기라고 바꿔주기만 하면 동일하다.

- 그 다음으로 url 설정을 해보자.
```python
from taskapp.views import TaskListView, TaskCreateView, TaskPreviousListView, TaskDetailView, \
    ChecklistCreateView, ChecklistUpdateView

urlpatterns = [
    ...
    # task 체크리스트 on/off 설정
    path('task/<int:task_id>/item/<int:check_id>/', ChecklistUpdateView.as_view(), name='check-item'),
    
```

- 가장 먼저 ChecklistUpdateView를 import 해준다. 그리고 수정하는 url를 설정.

- 이제 상세페이지인 task_detail.html로 돌아가서, 체크리스트 버튼에 a element를 추가해주자.
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
        </tr>
    {% endfor %}
    {% else %}
        <tr><td>체크리스트가 없습니다.</td></tr>
    {% endif %}
    </tbody>
</table>
```

- 이렇게 url 구분으로 경로를 지정할 때 parameter를 2개 이상 넘기려면, <a href="{% url 'check-item' object.id check.id %}"> -> 이렇게 띄어쓰기로 각 parameter를 구분해준다.
  - parameter 작성 순서는 url에 정의된 parameter 순서와 똑같이 맞춰주면 된다.













