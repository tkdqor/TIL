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


- 그리고 이어서
    
    
