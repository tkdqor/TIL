## 장고 기본 CBV API (Generic display views) (2)


### ListView 
- 1개의 모델에 대한 List 템플릿 처리 / 커스텀을 통해 꼭 템플릿이 아닌 다른 응답을 줄 수 있도록 할 수 있음.
- 모델명소문자_list 이름의 QuerySet을 템플릿에 전달
  - Post 모델이라면 post_list 이라는 이름의 queryset 생성. 또한 object_list라는 이름도 같이 활용할 수 있음.

- 페이징 처리가 내부적으로 지원됨.

```python
from django.views.generic	import ListView
from .models import Post

post_list1 = ListView.as_view(model=Post)

post_list2 = ListView.as_view(model=Post, paginate_by=10)
```

- post_list2	=	ListView.as_view(model=Post,	paginate_by=10) 이렇게 설정하면 -> 한 페이지에 10개씩 보겠다는 의미이다.
  - 이러한 방식으로 지정하면 우리가 GET인자로 ?page= 이렇게 page라는 이름의 인자를 ListView가 값을 가져와서 활용하게 된다. page라는 이름의 인자는 숫자문자열로 사용해야 한다. ?page=3 이면 3페이지를 처리하게 된다.
  - 이렇게 코드 한 줄로 페이징 처리를 해주는 서버단이 준비가 된다.

- 위의 코드를 상속을 통해 클래스로 구현해보면 아래와 같다.

```python
class PostListView(ListView):
    model	=	Post
    paginate_by	=	10
    
post_list3 = PostListView.as_view()


class PostListView(ListView):
    model	=	Post
    paginate_by	=	10
    
    def get_queryset(self):
        qs	=	super().get_queryset()
        qs	=	qs.filter(...)
        return qs
        
post_list4 = PostListView.as_view()
```

- 그리고 queryset 재정의가 필요하다면 get_queryset 메소드를 재정의 할 수 있다. 부모의 queryset를 super()로 호출해서 얻어내고 필터링한 결과를 반환하도록 한다.


### ListView 상속관계
- template_name이 지정되지 않았다면, 모델명으로 템플릿 경로 유추
- MultipleObjectMixin에서는 Paginator가 적용된 QuerySet 획득 -> paginate_by라는 설정이 들어있다면 paginator 인스턴스를 만들어서 페이징 처리를 하는 기능이 Mixin에 들어있다.


### 코드 예시
- https://github.com/django/django/blob/3.0.2/django/views/generic/list.py 해당 화면에서 여러 클래스들을 확인가능

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
    page_kwarg = 'page'
    ordering = None
    
    def get_queryset(self):
    # ...
    return queryset
    
    def paginate_queryset(self, queryset, page_size):
    # ...
    return (paginator, page, page.object_list, page.has_other_pages())    # 반환을 튜플로 진행
    
    
    def get_context_data(self, *, object_list=None, **kwargs):
        """Get the context for this view."""
        queryset = object_list if object_list is not None else self.object_list
        page_size = self.get_paginate_by(queryset)
        context_object_name = self.get_context_object_name(queryset)
        if page_size:
              paginator, page, queryset, is_paginated = self.paginate_queryset(queryset, page_size)
              context = {
                  'paginator': paginator,
                  'page_obj': page,
                  'is_paginated': is_paginated,
                  'object_list': queryset
              }
        else:
            context = {
                'paginator': None,
                'page_obj': None,
                'is_paginated': False,
                'object_list': queryset
            }
        if context_object_name is not None:
            context[context_object_name] = queryset
            context.update(kwargs)
        return super().get_context_data(**context)
```    
    
    
- 클래스 바로 밑에 들어있는 설정들이 바로 우리가 클래스.as_view() 를 호출할 때 모두 세팅할 수 있는 값들이다. post_list2 = ListView.as_view(model=Post, paginate_by=10) 이런식으로 말이다.
- 여러 부모 클래스들의 설정들은 as_view에서 모두 활용할 수 있다. 그래서 클래스를 볼 때, 이렇겍 들어가 있는 설정들을 읽어보자.
- python에서는 어떠한 값들을 콤마로 구별해서 소괄호로 감싸주게 되면 -> 튜플이 된다. 소괄호로 감싸지 않아도 튜플이다.
