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
    <div class="col">
        <div class="card text-white bg-secondary mb-3">
            <div class="card-header">{{ item.type }}</div>
            <div class="card-body">
                <h5 class="card-title">{{ item.title }}</h5>
                <p class="card-text">
                    <span class="badge bg-light text-dark">
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

- **이번엔 특이하게 object_list를 가지고 for문을 사용한다. ListView에서 우리가 따로 지정하지 않을 경우에 object_list의 형태로 조회된 결과가 담겨져서 넘어오게 된다.** 즉, ListView를 쓸 때의 예약어같은 느낌이다.

- 그리고 이어서 urls.py도 수정해주기

```python
from taskapp.views import TaskListView, TaskCreateView, TaskPreviousListView

urlpatterns = [
    ...
    # 이전 할 일 목록
    path('previous/', TaskPreviousListView.as_view(), name='previous'),

```

- 또한, 첫 페이지에서 접근할 수 있는 버튼을 위해 common.html 수정
```html

...

   <!-- 날짜 지난 일 확인 버튼 생성 -->
                    <li class="nav-item"> 
                        <a class="nav-link" href="{% url 'previous' %}">
                            <button class="btn btn-secondary">
                                이전 할 일
                            </button>
                        </a>
                    </li> 
```

- 버튼을 눌렀을 때 경로는 previous라는 이름을 가진 url를 설정.
- 여기까지 설정하고 브라우저에서 버튼을 클릭하면, Task 모델의 데이터가 잘 리스트로 보여지는 것을 확인할 수 있다.
  - 코드가 더 간결해졌다. 기존에는 우리가 리스트를 TemplateView를 상속받아서 띄웠는데, get_context_data라는 메소드를 굳이 사용하지 않고 ListView만 상속받아서 편리하게 활용할 수 있다.
  - ListView를 command로 클릭하고 / BaseListView도 클릭해보면 우리가 선언한 필드들이 어디서 유래가 되었는지 확인하는 게 좋다. BaseListView에 object_list가 등장한다. 이것이 우리가 template에서 object_list로 사용할 수 있게 하는 것이다. 이 리스트 뷰에서는 알아서 이러한 템플릿 변수가 생성이 되어있다는 것이다.


- 그런데, 한가지 문제는 지금 위와같이 설정하면 Task 모델의 모든 리스트가 뜨고 있다. 원래 우리는 due가 지난 이전 할 일들을만 보고싶다.
  - **이 때, django 모델 filter 조건 메소드를 사용해서 데이터를 조회할 때 조건을 걸 수 있다. 즉, 조건에 맞는 레코드들만 가지고 올 수 있다.**
  - **그냥 View라면 get 메소드나 get_context_data 메소드에 데이터를 return할 때 바로 필터링을 해서 return해주면 되지만 ListView는 더 편한 방법이 있다. 바로 queryset이라는 멤버를 그대로 활용하는 것이다.**

```python
from django.utils import timezone

class TaskPreviousListView(ListView):
    model = Task
    template_name = 'pages/task_previous_list.html'
    queryset = Task.objects.filter(due__lt=timezone.now())
    
    
```    
    
- **클래스 내부에 queryset를 설정해주자. queryset 다음으로는 django ORM의 queryset 구문을 그대로 활용하는 것이다. 현재 시간을 활용하기 위해 timezone를 import 해준다.**
  - **queryset = Task.objects.filter(due__lt=timezone.now()) -> 이 코드는, due라는 필드가 현재시간보다 작은, 이전 데이터만 가져오게 하는 것이다. 따라서 과거의 내역만 불러오게 된다. 이러한 현재시간 타임존에 맞는 적절한 시간을 구하기 위해서 우리가 timezone를 import 하는 것이라고 볼 수 있다. 그리고 django는 timezone이라는 모듈을 지원하기 때문에 현재 django 서버에서 세팅된 타임존 그리고 접속한 타임존을 잘 맞춰준다.**
  - queryset 관련 블로그 https://devvvyang.tistory.com/37

- django에서는 filter 메소드를 사용할 때 -> 여러 조건을 커버하기 위해서 언더바 2번을 입력한 다음 세부 조건 키워드를 입력하면 그 조건대로 동작하도록 지원하고 있다.
  - lt는 less than의 첫글자를 따서 입력하는 것이다.

- django의 
