## 리스트 View 클래스
- 데이터를 조회하는 패턴에는 크게 2가지가 있다. 레코드를 여러 개 조회하는 리스트 형태의 페이지이거나 / 하나의 데이터를 상세하게 보여주는 형태의 페이지
  - 장고에서는 이 2가지 형태의 조회 패턴을 지원하기 위해서 ListView와 DetailView를 지원한다. 그 중 하나인 ListView를 사용해서 지난 할일 목록을 조회해보자.

- ListView는 특정 모델을 바로 리스트 형태로 조회할 수 있게 해주는 ViewClass이다. 기본 형태는 model / template_name / queryset를 멤버 필드로 정의한다.

- views.py로 가서 실습을 해보자.
```python
from django.views.generic import TemplateView, FormView, CreateView, ListView

class TaskPreviousListView(ListView):
    model = Task
    template_name = 'pages/task_previous_list.html'
  
```

- due date가 지난 task들을 보여주는 클래스를 설정. 여기서 보여줘야 할 데이터의 원본인 모델을 멤버 필드로 설정. 그리고 보여줄 템플릿의 이름도 설정한다. 이후에 실제로 task_previous_list.html를 생성한다.

```html
{% extends 'common.html' %}

{% block content %}
<div class="row row-cols-2 row-cols-md-4 g-4 mt-2">
    {% for item in object_list %}
</div>
{% endblock %}




- 이번엔 특이하게 object_list를 가지고 for문을 사용한다.
