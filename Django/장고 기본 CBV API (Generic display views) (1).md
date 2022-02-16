## Generic display views 카테고리
- **DetailView, ListView가 있다.**
  - 먼저 DetailView는 SingleObjectTemplateResponseMixin을 상속받았다. 그래서 template 응답을 하는 걸 알 수 있다.
    - 직접적인 부모는 SingleObject와 BaseDetailView이다. 그리고 SingleObject는 TemplateResponseMixin를 상속받았다.
  - ListView의 경우에는 MultipleObjectTemplateResponseMixin을 상속받았다. 여기도 template 응답을 해줄 수 있다.

- 관련 공식 문서 : https://docs.djangoproject.com/en/3.2/ref/class-based-views/generic-display/


### DetailView
- 1개 모델의 1개 Object에 대한 템플릿 처리
- 템플릿 응답을 주는 해당 멤버 함수를 재정의 해서 템플릿 응답이 아닌, 재순응답이나 다른 응답을 주도록 변경할 수도 있다.
- **모델명소문자 이름의 Model Instance를 템플릿에 전달**
  - **우리가 Post 라는 이름의 모델이 있다면 -> 소문자 post라는 이름으로 template에다가 해당 모델 인스턴스를 전달해준다. ex) context = {'post': post} 이렇게 해서 전달해주는 것과 같이 DetailView가 해준다는 것.**

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

- 가급적이면 따로 template_name를 지정하지 않고 유추할 수 있도록 설정하는 것이 좋다.

- **SingleObjectMixin 클래스의 경우 : get_object라는 메소드가 정의되어 있다.** 이게 바로 post = get_object_or_404(Post, pk=pk) 이러한 코드를 의미한다. 함수 기반 뷰는 한 줄이지만, 클래스 기반 뷰는 엄청길게 코드가 되어있다. 여러 경우의 수를 처리해주느라 로직이 길어지는 것이다. 결론적으로 하나의 record를 찾아서 반환해준다. 이게 바로 post라고 볼 수 있다.
  - https://github.com/django/django/blob/3.0.2/django/views/generic/detail.py 해당 코드를 한 번 읽어보면서 이렇게 내부적으로 구현이 되고 있구나라는 것을 이해할 수 있도록 노력해보자.
  - self.kwargs 이 부분은 실제 클래스 기반 뷰에서 이미 정의가 되어있는 멤버 값이 된다. 타입은 딕셔너리이다. 우리가 url captured value, 즉 우리가 <int:pk> 처럼 url에서 전달받는 값이

```python
urlpatterns = [
    path('archives/<year:year>/<int:month>/<int:day>', views.archives_year),
 ]
 ```
 
   - 이렇게 되면, year, month, day라는 값들이 해당 View 함수에 전달되는 것인데 -> 이 때 전달될 때 딕셔너리의 형태로 넘어가게 된다는 것이다. 그래서 그 값이 self.kwargs에 저장되어 있다. 그 딕셔너리에서 pk.url.kwarg이라는 이름의 값을 가져와서 pk라는 값으로 활용할 수 있다. pk = self.kwargs.get(self.pk_url_kwarg)
   - 그래서, pk 또는 slug라는 값이 있을 때에는 queryset에서 filter를 해준다.
   - 또한, obj = queryset.get()를 통해 하나의 객체를 찾아내는 것이다. 이런식으로 get_object 메소드가 구현되어 있다.

* * *

- **get_queryset() 메소드**의 경우 : 안에 self.queryset은 클래스 속성에 none이라고 되어있다. 즉, post_detail = DetailView.as_view(model=Post, queryset=Post.objects.all()) 이렇게 queryset를 추가할 수도 있다는 것이다. 또는, post_detail = DetailView.as_view(model=Post, queryset=Post.objects.filter(is_public=True)) 이렇게 filter를 걸 수도 있다.
  - 그러면 우리가 DetailView를 구현할 때, 실제 view에서 처리할 때 is_public이 True인 범위내에서만 detail 처리를 한다는 것이다. 만약 1번 게시물의 is_public 필드가 False이면 url을 입력해도 나오지 않는다.        
  - 또한, 해당 메소드에서 default manager는 objects를 의미한다.
  - 유저마다 다르게 하고 싶다면, 무조건 상속을 받아야 한다.

```python
# post_detail = DetailView.as_view(
#    model=Post,
#    queryset=Post.objects.filter(is_public=True))

class PostDetailView(DetailView):
    model = Post
    # queryset = Post.objects.filter(is_public=True)
    
    def get_queryset(self):
        qs = super().get_queryset()
        if not self.request.user.is_authenticated:
            qs = qs.filter(is_public=True)
        return qs

post_detail = PostDetailView.as_view()
```

- 이렇게 상속을 받아서 클래스로 정의한 다음, get_queryset 메소드를 정의해준다. 보통 재정의할 때, super() 를 사용한다. -> 그래서 부모의 function를 호출해서 부모가 만들어준 queryset를 받고 우리가 filter를 하는 것이다.
- **함수 기반 View에서의 request인자는 -> 클래스 기반 View에서 self.request에 있다. 그리고 인증을 했다면, self.request.user.is_authenticated -> 이렇게 해서 현재 로그인된 유저의 인스턴스를 얻어올 수 있다.**
- if not self.request.user.is_authenticated: -> 만약 로그인이 되어있지 않다면 / qs = qs.filter(is_public=True) -> is_public 필드가 True인 게시물만 필터해서 저장. 즉, 로그인이 되어있지 않다면 공개된 것만 보라는 의미이다. 로그인이 되어있으면 if 조건에 걸리지 않으니까 모든 것을 다 볼 수 있다.
- ex) admin 아이디로 로그인 되어있으면 모두 다 조회가 가능하다.

* * *

- **get_context_data 메소드는, context가 있다.** 
  - context는 template 내에서 참조될 값들을 딕셔너리 형태로 준비해주는 것이다.
  - if self.object라는 건, get_object로 부터 획득한 object가 있다면 -> 그 object를 object라는 이름으로 저장해서 넘겨준다. 따라서, 우리가 object라는 이름으로 template내에서 실제로 참조할 수 있다.
  - 그 다음에, context_object_name = self.get_context_object_name(self.object) -> 이 코드는 get_context_object_name 이라는 함수가 위에 정의되어 있다. get_context_object_name 메소드를 보면, 해당 object가 모델의 인스턴스라면 모델명을 반환하도록 되어있다.
    - **따라서, ex) Post 모델은 object라는 이름으로도 template내에서 접근할 수 있고 / post라는 이름으로도 접근할 수 있다. 따라서 둘 다 되는 것이다.**

```python
def post_detail(request: HttpRequest, pk: int) -> HttpResponse:
    post = get_object_or_404(Post, pk=pk)
    return render(request, 'instagram/post_detail.html', {
         'post': post,
         'object': post,
    })
```

- 이렇게 사실은 object라는 변수를 template에서도 사용할 수 있다.
