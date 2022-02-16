## Generic display views 카테고리
- DetailView, ListView가 있다.
  - 먼저 DetailView는 SingleObjectTemplateResponseMixin을 상속받았다. 그래서 template 응답을 하는 걸 알 수 있다.
    - 직접적인 부모는 SingleObject와 BaseDetailView이다. 그리고 SingleObject는 TemplateResponseMixin를 상속받았다.
  - ListView의 경우에는 MultipleObjectTemplateResponseMixin을 상속받았다. 여기도 template 응답을 해줄 수 있다.

- 관련 공식 문서 : https://docs.djangoproject.com/en/3.2/ref/class-based-views/generic-display/


### DetailView
- 1개 모델의 1개 Object에 대한 템플릿 처리
- 템플릿 응답을 주는 해당 멤버 함수를 재정의 해서 템플릿 응답이 아닌, 재순응답이나 다른 응답을 주도록 변경할 수도 있다.
- 모델명소문자 이름의 Model Instance를 템플릿에 전달
  - 우리가 Post 라는 이름의 모델이 있다면 -> 소문자 post라는 이름으로 template에다가 해당 모델 인스턴스를 전달해준다. ex) context = {'post': post} 이렇게 해서 전달해주는 것과 같이 DetailView가 해준다는 것.

```python
def post_detail(request: HttpRequest, pk: int) -> HttpResponse:
...
```

- 그리고 우리가 View 함수에서 항상 request나 pk같이 인자를 받아서 넘겼었는데, DetailView는 pk나 slug라는 이름의 필드를 직접 내부적으로 찾아서 get_object_or_404를 하는 로직이 구현되어 있다.

```python
from django.views.generic	import DetailView
from .models import Post

# 첫번째 방식
post_detail1 =	DetailView.as_view(model=Post)


# 두번째 방식
class PostDetailtView(DetailView):
    model	=	Post

post_detail2 =	PostDetailtView.as_view()
```

- post_detail1	=	DetailView.as_view(model=Post) 처럼 받는 인자가 모델이다. 그래서 post_detail1처럼 설정할 수도 있고 
- post_detail2처럼 다른 클래스 기반 뷰와 동일하게 직접 상속을 받아서 모델을 지정해서 사용할 수도 있다.


### DetailView의 상속관계
- SingleObjectTemplateResponseMixin에서는 template_name을 지정할 수도 있는데, 만약 지정되지 않았다면, 모델명으로 템플릿 경로 유추하게 된다.
  - 위의 코드인 post_detail1 =	DetailView.as_view(model=Post) 여기에서도 template_name를 지정하지 않았다.
  - 사실 post_detail1 =	DetailView.as_view(model=Post, template_name='') 이렇게 지정할 수도 있다. 근데 지정하지 않으면, 내부적으로 모델명으로부터 이 모델이 속한 app이름도 알 수 있고,
    모델명으로부터 template이름도 알 수 있는 것이다.
