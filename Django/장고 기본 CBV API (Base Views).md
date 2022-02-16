## django 기본 class 기반 뷰 API 중, Base Views 카테고리

### Built-in CBV API
- Base views 카테고리 : View, TemplateView, RedirectView
  - 여기서 View가 모든 class 기반 View의 모체가 된다. 직접 이 클래스 기반 view를 사용할 일은 없다. 주요기능은, HTTP Method별로 지정 이름의 멤버함수를 호출토록 구현했다. 그래서 GET 요청이 오면 -> 실제 
    get이라는 함수를 호출해서 처리하도록 한다. 즉 handler를 호출하게 된다.
    - 클래스 기반 View는 항상 시작할 때, 클래스기반View.as_view() 이렇게 as_view라는 클래스 메소드 호출을 통해서 실제 View function를 만들어내는 것이다. 
    - https://github.com/django/django/blob/2.1/django/views/generic/base.py#L49 여기서 View라는 클래스를 확인할 수 있다.
  - TemplateView는 TemplateResponseMixin, ContextMixin, View 이렇게 3개의 클래스를 상속받았다. Mixin은 부모 클래스이기에 붙인 이름이고 직접 호출할 일은 없다.
    - ContextMixin의 경우에는 해당 클래스 기반 뷰에서 클래스 속성으로 extra_context가 정의가 되어있다면 그 대상을 키워드 argument(kwargs)에다가 업데이트하게 된다. 항상 딕셔너리 타입을 넣어줘야 한다.
    - TemplateResponseMixin 클래스의 경우에는 render_to_response라는 메인 메소드가 있다. **response_kwargs를 인자로 받는데, 지정되지 않았다면 'content_type'를 디폴트로 적용하겠다는 것.
    - 사용 ex) 지금 웹페이지 상에서 최상위 url인 localhost:8000/ 으로 들어가보면 아무것도 없는데, 이 최상위를 처리하는 url이 없기 때문이다. 그래서 프로젝트 디렉터리 urls.py에서 최상위를 처리하는 pattern을 넣어줄 수 있다. 밑에 예시를 보자. 

  - RedirectView는 View 클래스만 상속받았다.
- Generic display views 카테고리 : DetailView, ListView
- Generic date views 카테고리 : ArchiveIndexView, YearArchiveView, MonthArchiveView, WeekArchiveView, DayArchiveView, TodayArchiveView,
DateDetailView
- Generic editing views 카테고리 : FormView, CreateView, UpdateView, DeleteView
- 공식문서 : https://docs.djangoproject.com/en/3.2/ref/class-based-views/

- 이러한 여러 클래스 기반 뷰를 단순히 사용만 할 수 있겠지만, 내부 구현에 대해서 이해하게 된다면 더 효율적으로 활용할 수 있다.
  - 우리가 실제 웹 API를 만들게 될 때는 Django Rest Framework를 사용하게 된다. 그러면 굳이 CBV 몰라도 되는 거 아닐까라고 생각할 수 있으나, CBV를 잘 안다면 DRF를 더 잘 활용할 수 있다.
  - 그리고 리액트로 엔드-유저를 위한 페이지를 만들더라도 django의 form이나 admin를 활용해서 관리자 기능을 만들 수도 있다.


### TemplateView 예시
```python
from django.views.generic import TemplateView


urlpatterns = [
    path('', TemplateView.as_view(template_name='root.html'), name='root'),
    ...
```

- 이렇게 TemplateView를 사용해서 최상위 url를 처리할 수 있다. TemplateView.as_view() 여기 괄호안에 들어가는 것이 키워드 인자이다. 그렇게 입력된 것이 내부적으로 self.template_name이 된다.

```python
from django.views.generic import TemplateView


class RootView(TemplateView):
    template_name = 'root.html'


urlpatterns = [
    path('', RootView.as_view(), name='root'),
    ...
```    

- 혹은, 이렇게도 사용이 가능하다.


### settings.py - TEMPLATES
- 프로젝트 디렉터리 내부 settings.py를 열어서 보면 TEMPLATES라는 부분이 있다. 여기에 'DIRS'라는 항목에다가 설정을 넣을 수 있다.
- 우리가 지금까지는 template을 app 디렉터리 -> templates 디렉터리 -> app이름과 동일한 디렉터리 밑에 넣었다. 
- django의 template 시스템에서 django template loader라는 것이 있는데, app 디렉터리 loader와 / 파일 시스템 loader가 있다. app 디렉터리 loader는 app 디렉터리 안에 있는 templates에서 template를 찾아주는 것이고,
  파일 시스템 loader는 여기 setting.py - TEMPLATES - DIRS에서 정한 경로에서 template를 찾아주게 된다.
  - 그래서 app 디렉터리 loader에서 찾는 경로에서는 app과 관련된 template 파일을 구현하는 용도로 쓰고 / 특정 app에 속하지 않는 template의 경우에는 File system template loader에서 찾을 수 있도록 settings.py에서 설정한다.     

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            # File System Template Loader
            os.path.join(BASE_DIR, 'templates'),
        ],
```

- 보통은 위와 같이 프로젝트 내부 디렉터리 -> templates 경로를 지정해주는데, 

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            # File System Template Loader
            os.path.join(BASE_DIR, 'askcompany', 'templates'),
        ],
```

- 이렇게 프로젝트 디렉터리 -> 프로젝트 이름과 같은 디렉터리(settings.py가 있는 디렉터리) -> templates 디렉터리 안에 위치시킬 수도 있다. 이렇게 한다면 실제로 프로젝트 이름과 같은 디렉터리 밑에 template 디렉터리를 생성해야 한다.
  - 그리고 templates 안에다가도 root.html을 실제로 만들어주자.
  - 이러한 설정들을 마무리하고 다시 localhost:8000/ 으로 접속하게 되면 root.html에 내용이 출력된다.





