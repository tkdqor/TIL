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

- **결론적으로, Mixin Class들은 직접 GET이냐 POST냐에 따라서 GET 또는 POST라는 함수를 호출하는 방식, 즉 직접 이러한 요청을 핸들링하는 로직을 가지고 있지 않기 때문에 -> 다른 Generic View의 GET이나 POST 메소드와는 충돌하지가 않고, 요청을 핸들링할 수 있게 되는 것이다.**

* * *
- 그러나 예외적으로 **SingleObjectMixin**은 단일 데이터를 처리하기에 알맞게 get_object라거나 get_queryset등의 메소드들을 그대로 다 가지고 있다. 
  - 그래서 SingleObjectMixin를 command로 클릭해서보면, get_object나 get_queryset 메소드가 정의되어 있는 것을 확인할 수 있다.
  - 보통의 django Mixin 패턴에서는 GET 또는 POST 메소드를 선언한 클래스에 재정의하는 경우가 대부분이기 때문에 -> 여기서 get_object를 호출해서 queryset으로 Task를 선택하도록 

```python
self.object = self.get_object(queryset=Task.objects.all())
...
```

- 이렇게 전달해둔 것이다. 그러면 get_object는 SingleObjectMixin를 처리할 수 있게 되는 것이다. 그 다음에,

```python
return super().get(request, *args, **kwargs)
```

- 이렇게 super() 로 get를 호출하면 ListView가 호출이 되는 것이다. 

* * *
- **또한, 우리가 위에서 get_object를 호출해서 Task를 선택하도록 했지만 pk_url_kwarg로 pk 데이터를 task_id에서 받도록 선언했기 때문에, queryset내에서 pk가 일치하는 녀석을 get_object에서 알아서 처리를 해주게 된다. 그래서 지금은 self.object = self.get_object(queryset=Task.objects.all()) 이렇게 되어있지만, 사실은 -> self.object = self.get_object(queryset=Task.objects.all().get(pk=task_id)) 이런식으로 되어있는 것이다.**
  - 실제로, get_object를 command로 클릭해서 보면 수행하는 로직이 정해져 있다. 그래서 우리는 이런식으로 queryset를 전달해주는 것이다.

- 그 다음, 이 상태에서 GET 메소드가 호출되서 get_object가 호출되면 self.object의 object에는 -> DetailView와 유사하게 조회하고자 하는 데이터가 들어가 있게 된다. 이 상태에서 부모 클래스의 GET를 호출하게 되면, Mixin에서는 GET이 없기 때문에 ListView의 GET으로 처리가 되는 것이다. 
  - 이렇게 HTTP 요청을 핸들링하는 함수를 직접 구현한 뒤에 그 안에서 Mixin을 제공하는 함수를 직접 호출하면서 로직을 완성한 뒤에, 최종적으로 ListView에 있는 기존 요청 처리 메소드를 호출하는 방식으로 구성하는 것이다. 그리고 get_queryset 메소드도 override해서 -> ListView에서 처리될 queryset를 return하도록 했다. 우리가 직접 GET 내부적으로는 get_queryset를 사용하지는 않지만, ListView의 GET를 마지막에 호출해주기 때문에 ListView의 GET에서 queryset를 호출한 뒤, queryset를 이용해서 ListView를 구현하게 된다. 그리고 우리는 ChecklistItem를 queryset으로 지정했기 때문에 list 데이터는 ChecklistItem으로 결정된다.

* * *
- 이제, 위에서 설정한 Mixin이 어떻게 template으로 코딩되어야 할지 template 코드를 수정해보자. task_detail.html에 가보면,
```html
<!-- 체크리스트 출력 부분 -->
<table class="table">
    <tbody>
    {% if object_list %}              <!-- 하나의 task안에 checklists가 있다면 / 원래는 if checklists 이렇게 django 템플릿 랭귀지 사용 -->
    {% for check in object_list %}    <!-- checklists 안에 있는 check를 하나씩 빼서 진행 / 원래는 for check in checklists 이렇게 사용 -->
        <tr>
         ... 
```


- 기존에는 {% if checklists %} -> 이렇게 되어있는데 -> ListView에서는 기본적으로 queryset의 데이터를 object_list라는 template 변수로 매핑하기 때문에, 기존의 checklists로 넘기던 것을 object_list로 바꿔주면 된다. 
- 만약, pagination를 사용할 경우에는 paging을 template에서도 구현해주어야 하므로 우리가 배운 것처럼 page object를 사용해서 이터레이션과 페이지 링크를 그려주면 된다. 

- 그래서 template를 수정하고 다시 View로 돌아가서,

```python
# SingleObjectMixin를 활용해서 만든 TaskDetailView
class TaskDetailView(SingleObjectMixin, ListView):
    template_name = 'pages/task_detail/html'
    pk_url_kwarg = 'task_id'
    paginate_by = 10

    def get(self, request, *args, **kwargs):
        self.object = self.get_object(queryset=Task.objects.all())
        return super().get(request, *args, **kwargs)

    def get_queryset(self):
        return ChecklistItem.objects.filter(task=self.object).all()    
```




- 위와 같이 paginate_by를 10으로 설정해주면 이 ListView에 전달된 Checklist의 pagination이 지정되는 것이다. 그리고 난 다음에 다시 template으로 가서,

```html
<!-- 체크리스트 출력 부분 -->
<table class="table">
    <tbody>
    {% if page_obj %}              <!-- 하나의 task안에 checklists가 있다면 / 원래는 if checklists 이렇게 django 템플릿 랭귀지 사용 -->
    {% for check in page_obj %}    <!-- checklists 안에 있는 check를 하나씩 빼서 진행 / 원래는 for check in checklists 이렇게 사용 -->
        <tr>
...          
```
          
          
- 이렇게 해서 pagination도 직접 구현할 수 있게 된다.

* * *
- Mixin이라는 게 코드를 더 간소화하게끔 해주는 것도 있지만, 개발자 입장에서는 각 View class들과 Mixin class들의 구현체를 어느정도 이해를 하고 써야하기 때문에 django에 익숙하지 않거나 다른 개발자의 복잡한 구현체를 수정해야 하는 경우에는, 오히려 혼란스러워지는 경우도 있을 수 있다. 
- 그리고 잘못 사용하게 되면, 오히려 코드품질이 낮아지거나 의도한대로 동작하지 않을수도 있고, 모든 Mixin이 모든 View class와 호환되지도 않는다. 여기서 중요한 점은, 하나의 로직을 구현할 때에도 다양한 방식으로 구현할 수가 있는데 어떤 방법이 best인지는 우리가 직접 해보고 협업을 해보면서 자신만의 감을 찾아가야 한다. 대부분의 경우, 굳이 Mixin을 쓰지 않아도 구현할 수는 있으나 실제로 Mixin을 써야만 코드가 더 간결해지는 경우도 있다.
