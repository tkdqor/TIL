## 리스트 View 클래스
- 데이터를 조회하는 패턴에는 크게 2가지가 있다. 레코드를 여러 개 조회하는 리스트 형태의 페이지이거나 / 하나의 데이터를 상세하게 보여주는 형태의 페이지
  - 장고에서는 이 2가지 형태의 조회 패턴을 지원하기 위해서 ListView와 DetailView를 지원한다. 그 중 하나인 ListView를 사용해서 지난 할일 목록을 조회해보자.


### 
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
  - **django의 이러한 기능을 -> FieldLookup이라고 한다.** 장고 ORM의 QuerySet를 정의할 때 {필드명}__{조건} 이렇게 사용한다. 공식 문서는 https://docs.djangoproject.com/en/3.2/ref/models/querysets/#id4 여기를 참고하자.
  - 이 공식 문서를 보면서 여러 조건들을 확인할 수 있다. 여기서 i는 case를 ignore한다는 의미로 i를 붙여놓은 것이다.

* * *
### QuerySet

- **QuerySet** : QuerySet이란, 장고 ORM에서 데이터를 조회하는 명령어를 정의하는 것이다. 쿼리셋 단계에서는 실제 데이터베이스에서 데이터를 질의하지는 않는다. (lazy loading) 
  - 사실 이러한 ORM API를 사용해서 우리가 데이터를 조회하면, 데이터 자체가 바로 나오는 게 아니라 QuerySet이라는 클래스의 인스턴스가 return이 된다. 
  - django shell을 이용해 코드를 실행해보자.

```terminal
python manage.py shell

>>> from taskapp.models import Task
>>> from django.utils import timezone

>>> Task.objects.all()
<QuerySet [<Task: Task object (1)>, <Task: Task object (2)>, <Task: Task object (3)>, <Task: Task object (4)>, <Task: Task object (5)>]>

>>> Task.objects.filter(due__lt=timezone.now())
<QuerySet [<Task: Task object (1)>, <Task: Task object (2)>, <Task: Task object (3)>]>

>>> q = Task.objects.filter(due__gt=timezone.now())
>>> q = q.filter(title__contains='t')
>>> q = q.order_by('-due')

>>> q
<QuerySet []>
```

- 터미널에서 django shell을 실행. 
- 그리고 ORM 메소드인 objects를 사용해서 Task 모델의 데이터를 가져올 수 있다. 우리가 Task.objects.all() 이 코드를 실행했을 때 바로 출력이 되는데, QuerySet이라는 어떤 객체가 return이 되고 그 객체 하위에 모델 객체를 가지고 있는 형태로 출력이 된다. 
- 그래서 이 QuerySet이라는 것은 -> django ORM QuerySet API를 활용해서 여러 조건을 중첩시켜서 호출하는 것이 가능하다. 그 이유는 실제 QuerySet 코드로 인해서 QuerySet 인스턴스, 그러니까 위의 예시처럼 <QuerySet ...> 이러한 QuerySet의 인스턴스가 생성되는 시점에 대해서 바로 데이터베이스에 대한 조회가 실행되는 것이 아니라, 실제 데이터에 접근해야하는 시점에서야 데이터베이스에서 데이터를 불러오는 동작 방식이기 때문이다. 이를 QuerySet의 construction 시점이랑 evaluation 시점이 다르다고 표현한다.
  - ex) q = Task.objects.filter(due__gt=timezone.now()) -> 이 코드가 실행되서 q에 들어가는데 그럼 이 q에는 뭐가 들어가 있을까? q에는 그냥 QuerySet이라는 인스턴스만 있다. 그리고 이 코드가 실행된 지금까지는 실제로 데이터베이스에 접근되어있는 상태가 아니다. 실제로 이 filter가 동작을 해서 이 조건을 만족하는 레코드가 무엇이 있는지는 아직 모른다. 아직 결정된 상태가 아닌 것이다. 그래서 이 상태에서는, 바로 q에다가 이어서 계속 filter를 걸어 더 복잡한 조건을 완성시키는 게 가능하다.
  - ex) 이어서 filter라는 ORM를 이용해서 q = q.filter(title__contains='t') 이렇게도 사용가능하다. 그리고 q = q.order_by('-due') 이렇게 due 필드를 역순기준으로 정렬할 수도 있다.
  

### QuerySet의 Evaluate 시점(데이터베이스 히트)
- 그러면, 언제 실제로 DB에 접근해서 평가된다는 시점이 발생할까? 다음 6가지 상황일 경우를 의미한다.
  - 이터레이션 / 슬라이싱 / 픽클링(pickling) / 캐싱(caching) / len() 호출 / list(), boll() 로 반환 / 파이썬 인터프리터에서 출력할 때

- 이터레이션은 우리가 템플릿이나 for문을 실행할 때, loop문을 돌 때 QuerySet에 있는 데이터를 가지고 와서 출력을 하거나 데이터를 꺼내야 하는 경우를 의미한다. 그래서 그 데이터가 뭔지 알아야 되고 실제로 QuerySet의 결과가 개수가 몇 개인지 확정이 되어야 한다. 그럴려면 당연히 데이터베이스에 히트를 해야 데이터베이스에 이 조건으로 존재하는 데이터의 개수를 확인할 수 있기 때문에 이터레이션 시점에서는 당연히 히트를 쳐야 한다.
- 슬라이싱의 경우에도 QuerySet의 결과를 알고 있어야 한다. 리스트를 쪼개거나 분할하는 것을 의미할텐데, 슬라이싱을 할 때도 이 조건에 해당하는 실제 데이터 수가 몇 개인지 확인을 해야 데이터를 쪼개거나 짜르거나 할 수 있다. 그래서 슬라이싱의 경우에도 데이터베이스를 히트하게 된다.
- 픽클링과 캐싱은, 먼저 픽클링은 직렬화(Serialization) 시점에도 히트가 발생한다는 것이다. 
  - q[0]와 같이 특정 인덱스를 찾아서 슬라이싱을 한다던가, list(q) 처럼 QuerySet 결과를 바로 리스트로 리턴하는 경우, 즉 어떤 데이터를 강제로 리스트로 변환해서 직렬화하는 작업을 하게 되는데 그 시점에도 히트가 발생하고 json으로 바꾸거나 문자열로 변환해서 출력하거나 등 이런 것들이 전부 픽클링 사례에 속한다.
- len() 호출은 어떻게보면 픽클링의 한 예시라고도 할 수 있다. len()는 당연히 리스트의 개수를 출력하는 함수니까 당연히 데이터의 개수를 알고있어야 한다. 
- list(), bool() 이렇게 어떤 데이터를 리스트로 변환하거나 boolean으로 변환하는 경우도 히트가 발생한다.
- 마지막으로 django shell이나 python 인터프리터에서 출력할 때도 동일하게 evaluation이 이루어지게 된다. (print())
  - 위에서 봤던 것처럼, QuerySet 인스턴스 만으로 <QuerySet [<Task: Task object (1)>, <Task: Task object (2)>, <Task: Task object (3)>]> -> 이렇게 그 조건에 해당하는 것들을 알 수 있다. 엄밀히 말하면 인터프리터에서 실행이 될 때는 강제적으로 QuertSet이 실행이 되긴 한다. 이렇게 프린트를 해줘야 하기 때문에 그럴려면 어떤 QuerySet의 결과를 알고있어야지 결과가 이럴 것이다 라고 미리 프린트를 해주는 역할을 할 수 있는 것이다.
  
- 그래서, 이렇게 평가시점의 차이 때문에 QuerySet API를 마구 이어서 호출을 해도 별 문제는 없고 chaining이라고 부르기도 한다. 다른 프로그래밍에서도 등장하는 기법이다. 
  - 그리고 실제 데이터에 접근하는 시점과 Query가 정해지는 시점과 다르다는 것을 두고 lazy loading이라고 부르기도 한다.
