## CreateView
- 할 일을 생성할 때 CreateView를 사용해보기.
- django에서는 FormView를 확장해서 몇가지 View 클래스를 더 제공한다. 데이터의 생성, 수정, 삭제에 맞게 CreateView, UpdateView, DeleteView를 제공한다.
  - django는 CRUD를 쉽게 할 수 있도록 설계원칙에 이러한 개념이 녹아있다. 


### CreateView 사용해보기
- 기존의 앱 내부 views.py 수정하기

```python
from django.views.generic import TemplateView, FormView, CreateView

...
class TaskCreateView(CreateView):
    model = Task
    fields = ['title', 'type', 'due']
    template_name = 'pages/task_create.html'
    success_url = '/'





```


- 먼저 CreateView를 import 해주기. 그리고 기존의 FormView를 상속받은 것을 수정해서 -> CreateView를 상속받아서 클래스를 설정해보기.
  - form_valid 메소드 부분은 삭제하자.

- **CreateView는 기존의 FormView를 더 줄인 것이라고 볼 수 있다. FormView는 우리가 forms.py를 생성해서 -> 모델 Form을 직접 구현도 하고 필드도 다 지정을 해야 했다.
  - CreateView는 forms.py에 설정한 내용들을 그대로 합쳐서 편리하게 사용할 수 있다. forms.py에서 설정한 Meta 클래스를 동일하게 넣어준다.
  - CreateView가 Form를 대체하기 때문에 form_class라는 변수는 지워버리면 된다.
  - 이렇게, CreateView를 상속만 받고 처리할 모델 / 필드 / 템플릿 / 리디렉션 주소만 설정해주면 끝이다.
