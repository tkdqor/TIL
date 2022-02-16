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
    - 페이지를 이동하는 것이다. 
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


### RedirectView 관련
- 우리가 웹에서 페이지를 이동하는 방식이 크게 2가지가 있다.
  - 1번째는 서버 응답에서 redirect 상태 코드를 주는 것이다. 200 상태 코드는 ok이고 404는 Page not found이고 500번대는 server에러 이다.
    - 301, 302 응답은 server 응답에서 페이지를 이동하라는 응답이 된다. 이렇게 되면 브라우저가 알아서 이동하는 방식이 있다.
  - 2번째는, 자바스크립트 단에서 자바스크립트 API에서도 주소를 이동시키는 방식이 있다.
    - 자바스크립트로 이동할 때에는 개발자도구를 열고, console을 선택한 후

```console
location.href
'http://localhost:8000/'

location.href = "http://www.naver.com"
```

- location.href를 입력하면 현재 값이 나온다. 근데 여기에 다른 주소를 대입해서 enter를 하면 아예 그 페이지로 화면이 바뀌게 된다. -> 이렇게 자바스크립트로도 주소를 변경할 수 있다.


- 그래서 서버 응답에서 주소를 변경하는 경우, permanent라는 설정값이 있는데 디폴트가 False이다. 거짓인 경우에는 302 응답을 준다. 즉, 임시 이동을 의미한다. 검색 엔진 원본은 그대로 있다. / 참으로 주면 301 응답을 주는데 영구적인 이동을 의미한다. 그래서 검색 엔진에 영향을 끼친다. 검색 엔진 입장에서 해당 url 리소스가 지금 이동할 페이지로 완전히 변경되었구나라고 인식해서 내부적으로 인덱싱 처리를 다르게 한다.

- RedirectView에서 url은 어디로 이동할 것인지 설정. URL 문자열을 줄 수 있다. 같은 host name에서 이동하는 경우에는, host name을 생략하고 나머지 경로에서 절대 경로로 주는 것이 추천된다.
  - pattern name의 경우는 url reverse를 수행할 문자열을 지정해준다.
  - query string은 redirect 될 때, 주소 뒤에 ? 다음에 붙어있는 query string을 그대로 넘길것인지 아닌지 여부를 결정하는 인자이다.

```python
from django.views.generic import TemplateView, RedirectView


urlpatterns = [
    # path('', TemplateView.as_view(template_name='root.html'), name='root'),
    path('', RedirectView.as_view(url='/instagram/'), name='root'),
```

- 이렇게 예시로 사용해볼 수 있다. url 인자를 넣으면, localhost:8000/으로 접속해도 뒤에 url에 붙은 주소로 이동시키게 된다.

```python
urlpatterns = [
    # path('', TemplateView.as_view(template_name='root.html'), name='root'),
    path('', RedirectView.as_view(
        pattern_name='instagram:post_list',
        ), name='root'),
```

- 또한, 이렇게 pattern_name이라는 인자를 넣어서 instagram앱의 post_list라는 name를 가진 url pattern를 지정해줄 수도 있다. django에서는 이렇게 pattern name 지정이 선호된다.

- **강의 자료보고 클래스 하나씩 뜯어보고 어떻게 기능이 구현되는지 살펴보자.**
