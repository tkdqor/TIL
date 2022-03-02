## 기본 View 클래스, 템플릿 View
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


- 위와 같이 설정하고 난 후, 이제는 urls.py로 가보자. 프로젝트 디렉터리 내부에 있는 urls.py를 설정하자.
  - 뷰 클래스로 정의한 클래스는 -> as_view라는 메소드가 있다. 그걸 호출해서 처리하게 된다. 

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



12:17
