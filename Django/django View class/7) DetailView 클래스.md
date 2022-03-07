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
    ...
    # task 1개 조회
    path('task/<int:task_id>/', views.index, name='view-task'),
    
``` 
 
- path parameter로 task_id가 선언되어 있는데 이 task_id가 위에 view에서 pk_url_kwarg이라고 생각하면 된다.
- 여기까지만 설정을 해도 task_id로 전달된 task를 조회해서 출력하는 View code는 이걸로 끝이다.
  - 하지만 추가로 우리는 연결된 체크리스트도 출력해야 되기 때문에, DetailView 클래스 내부에 context 데이터에 체크리스트를 담아서 같이 넘겨줘야 한다. 다른 generic View처럼 get_context_data 메소드를 
    구현해서 기존 클래스의 메소드를 호출하고, 그 context에 체크리스트를 포함시켜서 return 해주면 된다.
 
