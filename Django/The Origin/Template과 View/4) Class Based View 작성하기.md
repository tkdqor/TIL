## Class Based View 작성하기
- django에 이미 내장된(작성된) 클래스 코드들이 많이 있다.
- https://docs.djangoproject.com/en/4.0/ref/class-based-views/ 해당 공식 문서에서 핵심적으로 봐야 할 View는 

<img width="449" alt="image" src="https://user-images.githubusercontent.com/95380638/163141634-d357acf1-b3d6-4726-8d31-ac7158c9914e.png">

- 다음과 같다. 우리가 여기서 ListView를 사용해보면서 어떤 차이가 있는지 알아보자.


### ListView
- 우리가 작업했던 liongram 디렉터리 내부에 있는 posts App View에서 새롭게 코드를 입력해보자.

```python
# 클래스 뷰를 위한 상속
from django.views.generic.list import ListView

...
class class_view(ListView):
    pass
```

- **먼저 generic 패키지에서 ListView를 import 해주자. 그리고나서 우리가 새로운 클래스 뷰를 작성할 때 이 ListView를 상속받아주자.**
  - 이 ListView를 command로 클릭해보면, 

```python
class ListView(MultipleObjectTemplateResponseMixin, BaseListView):
    """
    Render some list of objects, set by `self.model` or `self.queryset`.
    `self.queryset` can actually be any iterable of items, not just a queryset.
    """
```

- 이렇게 ListView 자체도 2개를 상속받는다. 이 BaseListView도 들어가보면 --> MultipleObjectMixin과 View를 상속받는다. 그리고 또 MultipleObjectMixin를 들어가보면 

```python
class MultipleObjectMixin(ContextMixin):
    """A mixin for views manipulating multiple objects."""

    allow_empty = True
    queryset = None
    model = None
    paginate_by = None
    paginate_orphans = 0
    context_object_name = None
    paginator_class = Paginator
    page_kwarg = "page"
    ordering = None

    def get_queryset(self):
        """
        ...
```

- 이렇게 클래스가 정의되어있다. **결국, 제일 중요한 건 모델이다.** 

* * *
- 다시 우리의 views로 돌아가서 코드를 작성해보자.

```python
# 클래스 뷰를 위한 상속
from django.views.generic.list import ListView
from .models import Post

...
class class_view(ListView):
    model = Post
    template_name = 'cbv_view.html'
```


- **일단 먼저 우리가 정의해놓은 모델 중 Post 모델을 Import 해주자. 그리고 우리가 정의한 클래스 뷰에서 model을 Post로 정의해주면 된다. 또한 추가로 template_name이라는 속성에 우리의 template 하나를 입력해주면 된다.** 
- 그리고 templates 디렉터리에 해당 template를 생성하자. 해당 template에서는

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    {% for object in object_list %}
        {{ object }}
    {% endfor %}
</body>
</html>
```

- **이렇게 object_list라는 변수에서 object를 하나씩 for문으로 뽑고 출력해보자. ListView는 이런식으로 따로 변수를 정하지 않고 object_list라는 변수를 template에서 사용할 수 있다.**

- 추가로 urls.py에서는

```python
from posts.views import url_view, url_parameter_view, function_view, class_view

urlpatterns = [
    path('admin/', admin.site.urls),
    path('url/', url_view),
    path('url/<str:username>/', url_parameter_view),
    path('fbv/', function_view),
    path('cbv/', class_view.as_view()),
]
```

- **이번에는 우리가 정의한 class_view를 먼저 import하고 path에서는 class_view.as_view() 이렇게 해당 클래스의 as_view함수를 사용해줘야 연동이 된다.**
- 이러한 설정 다음에 브라우저에 해당 url를 입력해보면,

<img width="572" alt="image" src="https://user-images.githubusercontent.com/95380638/163146117-c95b4fb8-e6d0-4e20-bae6-6264d4f8343a.png">

- 다음과 같이 Post 모델의 인스턴스를 하나씩 뽑아서 출력해준다.




