## Mixin
- Mixin은 하나의 Generic View 클래스와 다른 Generic View 클래스의 특성을 같이 조합해서 쓰고 싶을 때 사용하는 개념이다. 그냥 봤을 때는 다중상속처럼 보이지만, 객체지향 패턴에 믹스인 패턴에서 가져온 개념이기
  때문에, 엄밀히 말해서는 어떤 Generic View 클래스를 상속해서 클래스 형태의 View를 구현할 때 다른 Generic View의 특성을 포함하도록 하는 개념이다.
- 그러니까 상속이 아니라, 포함으로 해석할 수 있는 디자인 패턴이다. A라는 클래스를 상속하면서 동시에 B라는 클래스의 특성만 상속하는 것이다. django에서는 이러한 패턴을 지원하기 위해서 여러 Mixin 클래스들을 지원한다.
  - Mixin 기법을 쓰고 싶다면 선언문에 MixinClass와 일반 View Class를 상속받는 형태로 선언을 해야한다.

- 대표적인 Mixin클래스들로는 다음과 같다.
  - TemplateResponseMixin은 TemplateView의 특성을 가지고 있고, SingleObjectMixin은 DetailView / MultipleObjectMixin은 ListView / FormMixin은 FormView / ModelFormMixin은 CreateView와 UpdateView의 특성을 가지고 있다.
  - 이외에 Mixin은 공식문서에서 확인가능하다.
  - 우리는 SingleObjectMixin과 ListView를 결합해서 이전에 만들었던 TaskdetailView를 수정해보도록 하자.

* * *
### 실습하기
- Views.py에서 SingleObjectMixin를 import하고 기존 코드를 수정해보자.

```python
from django.views.generic.detail import SingleObjectMixin

# 기존의 DetailView를 상속받은 클래스
# class TaskDetailView(DetailView):
#     model = Task
#     template_name = 'pages/task_detail.html'
#     pk_url_kwarg = 'task_id'

#     def get_context_data(self, **kwargs):
#         context = super().get_context_data(**kwargs)
#         context['checklists'] = ChecklistItem.objects.filter(task=self.object).all()
#         return context


# SingleObjectMixin를 활용해서 만든 TaskDetailView
class TaskDetailView(SingleObjectMixin, ListView):
    template_name = 'pages/task_detail/html'
    pk_url_kwarg = 'task_id'

    def get(self, request, *args, **kwargs):
        self.object = self.get_object(queryset=Task.objects.all())
        return super().get(request, *args, **kwargs)

    def get_queryset(self):
        return ChecklistItem.objects.filter(task=self.object).all()  
```



- 이렇게 SingleObjectMixin과 ListView를 둘 다 상속받는 형태로 만들어준다. 이렇게 되면 SingleObjectMixin의 특성을 가진 클래스가 생성이 되고, 그 때 ListView의 메소드들을 실행할 수 있게 된다. 그래서 2개가 합성이 되어 구현을 할 수 있게 된다.
  - 일단 우리가 ListView를 상속받았기 때문에 ListView 기존에 있었던 get_queryset 메소드를 활용할 수가 있게 된다. 이 get_queryset를 override해서 여기에 ChecklistItem 모델의 filter를 적용할 수 있다.
  
- **SingleObjectMixin의 구현체를 command 클릭으로 따라가보자. 그러면, 상위 클래스로 이동하더라도 어느 Mixin 클래스이건 GET 요청을 핸들링하는 GET 메소드가 없다는 점이 기존 View 클래스들과 차이점이라고 할 수 있다. get_object 메소드는 있지만, 그냥 get 메소드는 없다.**
  - **django에서 Mixin 클래스는 -> GET이나 POST같이 직접적으로 request를 받아서 처리하는 패턴의 메소드가 없다. 그런데, ListView는 GET이나 이런식의 실제로 요청을 받아서 직접적으로 처리하는 녀석이 존재하는 클래스이다.** 
  - **django에서 Mixin은 어떤 특성을 받아서 쓸 수 있다고 했는데, django에서 이를 잘 구현하기 위해서 객체지향의 Mixin 패턴에서는 실제로 어떤 로직이 실행되고 진입점이 되는 GET이나 POST같은 요청을 처음으로 받아서 실제 주체가 되서 수행되는 메소드는 ListView와 같이 구체화된 Generic Class에서 실행이 된다. Mixin Class에서는 보조적으로 GET이나 POST에서 사용되는 여러 참조되는 필드나 인터페이스들을 제공하는 역할만 하게 되는 것이다.**
  - 그래서 위의 예시코드처럼 get_queryset 메소드를 override하게 되면 ListView의 GET에서 실행이 될 때, get_queryset이 알아서 호출이 된다던가 해서 실행이 되는 것이다.


