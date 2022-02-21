## URL Reverse를 통해 유연하게 URL 문자열 및 응답 생성하기

### URL Dispatcher
- urls.py 변경만으로 “각 뷰에 대한 URL”이 변경되는 유연한 URL 시스템

```python
# "/blog/", "/blog/1/" 주소로 서비스하다가
urlpatterns = [
    path('blog/', blog_views.post_list, name='post_list’),
    path('blog/<int:pk>/', blog_views.post_detail, name='post_detail'),
]


# 다음과 같이 변경을 하면,
# 이제 "/weblog/", "/weblog/1/" 주소로 서비스하게 됩니다.
urlpatterns = [
    path('weblog/', blog_views.post_list, name='post_list’),
    path('weblog/<int:pk>/', blog_views.post_detail, name='post_detail'),
]
```


### URL Reverse의 혜택
- 개발자가 일일이 URL을 계산하지 않아도 된다. URL이 변경되더라도, URL Reverse가 변경된 URL을 추적.
  - 만약, 위의 url pattern에 대응해서 html 파일의 a element에서 <a href="/blog/">...</a> 이렇게 설정을 했다가 url이 바뀌게 된다면 -> 이런 항목들을 찾아서 하나하나 다 변경해줘야 한다. 그래서 보통 
    주소를 한 번 설정하면 잘 안바꾸려고 한다. 왜냐하면 어디에서 참조되고 있는지 너무나도 찾기 힘들기 때문이다.
  - **반면에 URL Reverse는 a element와 같은 url을 직접 계산하지 않아도 django에게 이러한 url 문자열 계산을 위임하는 것이다.**

- django에는 App이라는 개념이 있는데, App은 재사용이 가능한 django application를 app의 형태로 패키징하는 것이다. 그래서 만약 블로그 App를 구현했다가 새로운 다른 프로젝트에서 블로그 기능을 쓸 수 있다.
  블로그 폴더를 복사해서 옮기고, settings.py - INSTALLED_APPS에다가 그 블로그 App를 추가하고 프로젝트의 urls.py에다가 include해서 해당 App에 대한 urls.py를 include해주면 끝이다. 그리고 필요하면 migration를 해준다. 
  - 이런 경우, 우리가 원한다면 url를 blog로 시작하는 게 아니라 weblog로 수정할 수 있는 것이다.

- 프로젝트 내부 urls.py에 자동생성 되어있는 admin 페이지 url도 정해져 있는 게 아니라서 바꿔주면 django가 알아서 역추적하여 설정해준다.

- 만약 /blog/api/post/1001/comments/100/ 과 같은 URL이 있다면 외워서 쓸 것 인가 -> URL Reverse로 그러지 않아도 된다는 것.
  - blog, api, post에 그 다음은 post의 pk일 것이고, 그 다음은 comments, comments의 pk...



### URL Reverse를 수행하는 4가지 함수 (1)
- **url 템플릿태그** 
  - django template에서만 사용할 수 있는 함수 태그.
  - {% url 인자 %} 이렇게 사용.  ex) {% url 'posts:index' %} -> posts 앱의 index 이름을 가진 url pattern

- **reverse 함수**
  - .py인 python 파일에서 사용.
  - 매칭 URL이 없으면 NoReverseMatch 예외 발생

- **resolve_url 함수**
  - .py인 python 파일에서 사용.
  - 매핑 URL이 없으면 "인자 문자열"을 그대로 리턴
  - 내부적으로 reverse 함수를 사용

- **redirect 함수**
  - .py인 python 파일에서 사용.
  - 매칭 URL이 없으면 "인자 문자열"을 그대로 URL로 사용
  - 내부적으로 resolve_url 함수를 사용

- URL Reverse를 수행하는 제일 low-level 함수는 reverse 함수이다. 그리고 reverse 함수를 조금 더 쓰기 쉽게 바꿔준 것이 resolve_url 함수이다. 그리고 resolve_url 함수에서 기능을 조금 더 첨부한 것이 redirect 함수가 된다.
- 반환값은, resolve_url 함수는 어떠한 url 문자열을 만들어준다. reverse는 이동할 실제 url 문자열을 만들어준다. Redirect 함수는 HttpResponse의 인스턴스를 만들어준다. 즉, View에서 바로 쓸 수 있는 것이다.
- url 템플릿 태그에서도 reverse 함수가 사용이 된다.  


### URL Reverse를 수행하는 4가지 함수 (2)
```html
{% url "blog:post_detail" 100 %} -> 문자열 URL
{% url "blog:post_detail" pk=100 %}
```

```python
reverse('blog:post_detail', args=[100]) -> 문자열 URL
reverse('blog:post_detail', kwargs={'pk': 100})

resolve_url('blog:post_detail', 100) -> 문자열 URL
resolve_url('blog:post_detail', pk=100)
resolve_url('/blog/100/')

redirect('blog:post_detail', 100) -> HttpResponse 응답 (301 or 302)
redirect('blog:post_detail', pk=100)
redirect('/blog/100/')
```

- 일단 기본적으로 path마다 name을 지정해야 한다. 그래서 'app이름:url이름' 이런식으로 설정하면 된다. 만약 app이름을 설정하지 않는다면 중복될 수 있다. posts앱의 index url과 products앱의 index url 이렇게 중복이 될 수 있기 때문에 app이름을 지정해야 한다. 
- 그리고 reverse와 resolve_url은 인자가 다르다. 
  - resolve_url에서는 우리가 url pattern에서 pk 등 URL Capture value를 지정하는데, 이렇게 지정한 것들을 resolve_url에서 인자로 하나만 있으면 1개, 여러개라면 순서대로 하나씩 입력하고 괄호를 닫아주면 된다. resolve_url('dd', 100, ..., ...) 또는 키워드 식으로 지정해도 된다. resolve_url('~', pk=100, ..=111)
  - reverse의 경우에는 인자를 항상 args라는 이름으로 지정해줘야 한다. args=[100] 또는 키워드 인자의 경우는 키워드 arguments인 kwargs={'pk': 100} 이렇게 사전으로 넘겨줘야 한다. 


```terminal
python manage.py shell

In [1]: from django.urls import reverse

In [2]: from django.shortcuts import resolve_url

In [3]: reverse("instagram:post_list")
Out[3]: '/instagram/'

In [4]: reverse("instagram:post_detail")
NoReverseMatch: Reverse for 'post_detail' with no arguments not found. 1 pattern(s) tried: ['instagram/(?P<pk>[0-9]+)/$']

In [5]: reverse("instagram:post_detail", args=[123])
Out[5]: '/instagram/123/'

In [7]: resolve_url("instagram:post_detail", 123)
Out[7]: '/instagram/123/'

In [8]: resolve_url("instagram:post_detail", pk=123)
Out[8]: '/instagram/123/'
```

- reverse("instagram:post_detail") -> 이렇게만 입력하면 reverse 함수에 인자가 없다고 나온다. 그래서 reverse("instagram:post_detail", args=[123]) 이렇게 인자를 넣어줘야 한다.

- redirect 함수도 resolve_url 함수와 같은 형태로 활용할 수 있는데, 응답이 HttpResponse 응답이 된다. (301 or 302) 디폴트로는 임시이동 응답이고, 추가로 지정해서 영구이동 상태코드를 반환할 수도 있다.

* * *
```html
 <a href="/instagram/{{ post.pk }}/">
     {{ post.message }}
 </a>

<a href="{% url 'instagram:post_detail' post.pk %}">
     {{ post.message }}
</a>
```

- template에서도 1개의 글 페이지에 상세 페이지 버튼을 위와 같이 url 설정을 통해 만들 수 있다. 하지만 첫번째처럼 url를 구성하는 건 django 스타일은 아니다. 만약 프로젝트 디렉터리 루트에 있는 urls.py의 url이 instagram이 아니라 다른 것으로 바뀌면, 모든 a element의 url도 바꾸어야 한다.
- 그래서 두번째처럼 django template tag를 사용해서 이용하자. 


## 모델 객체에 대한 detail 주소 계산
- 우리가 만약 Post와 같이 하나의 모델을 만들었다면, 해당 모델 하나의 레코드에 대한 디테일 페이지를 항상 만들게 된다. 
  - 그래서 template이든 어떤 코드에서든 detail url을 계산할 일이 많아진다.

- 매번 다음과 같은 코드로 할 수 있겠지만,
```python
resolve_url('blog:post_detail', pk=post.pk)
redirect('blog:post_detail', pk=post.pk)
{% url 'blog:post_detail' post.pk %}
```

- 아래와 같이 짧게 사용할 수도 있다.
```python
resolve_url(post)
redirect(post)
{{ post.get_absolute_url }}
```

- 여기서는 그냥 모델 객체를 넘겨주면 된다. -> Post 모델에다가 뭔가를 구현해줘야 한다. 


### 모델 클래스에 get_absolute_url() 구현
- 위에처럼 디테일 url를 구현하려면 -> 모델 클래스에 get_absolute_url() 이라는 멤버함수를 구현하면 된다. 이 함수를 구현하게 되면, resolve_url()과 redirect()에서 get_absolute_url()를 활용할 수 있도록 개발이 되어있다. 정확하게는 resolve_url()에 구축이 되어있고, redirect()는 resolve_url() 것을 가져다가 쓰는 것이다.
- resolve_url 함수는 가장 먼저 get_absolute_url() 함수의 존재여부를 체크하고, 존재할 경우 reverse를 수행하지 않고 그 리턴값을 즉시 리턴
- template에서는 {{ post.get_absolute_url }} 이렇게 호출할 수 있도록 하는 것이다.

- resolve_url() 함수를 살펴보면,  https://github.com/django/django/blob/3.0.2/django/shortcuts.py
```
# django/shortcuts.py

def resolve_url(to, *args, **kwargs):
    if hasattr(to, 'get_absolute_url'):
        return to.get_absolute_url()
# 중략
try:
    return reverse(to, args=args, kwargs=kwargs)
except NoReverseMatch:
    # 나머지 코드 생략
```

- resolve_url()에서 우리가 첫번째 인자로 pattern name을 받을 수도 있고, 이동할 문자열 url를 받을 수도 있고, 모델 객체를 받을 수도 있다. 대상이 어떤 타입인지는 모르겠지만, 만약 그 대상이 'get_absolute_url'이라는 이름의 attribute가 있다면 그 함수를 호출해서 반환값을 바로 return 한다는 것이다. 

- 그래서, 어떤 모델에 설정하는 구조는
```python
from django.urls import reverse

class Post(models.Model):
    # 중략
    def get_absolute_url(self):
    return reverse('blog:post_detail', args=[self.pk])
```

- 이렇게 어떤 모델에서 detail View를 구현하는 것은, 위와 같이 def get_absolute_url(self): 이렇게 설정을 모델 클래스 내부에 해주는 것으로 진행된다. 그리고 return 뒤에 reverse로 지정한다.
  - 모델 클래스에 지정하게 되면 -> resolve_url(post) / redirect(post) 이와 같은 방법으로 바로 detail 페이지의 url를 획득할 수 있다.

```python
from django.urls import reverse

class Post(models.Model):       # 우리가 원하는 데이터베이스에 저장하고 싶은 내역대로 설계를 해서 사용하면 된다.
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    message = models.TextField()    # 기본 default 값이 blank=False이다.
    photo = models.ImageField(blank=True, upload_to='instagram/post/%Y/%m/%d') # 기본적으로 blank를 설정해줘야 migration이 진행됨
    tag_set = models.ManyToManyField('Tag', blank=True) 
    is_public = models.BooleanField(default=False, verbose_name='공개여부')      # 기본값은 공개하지 않는 것이고, 필드명을 '공개여부'로 수정
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
    
    # detail url를 더 쉽게 얻을 수 있게 Post 모델 클래스 내부에 설정하는 코드
    def get_absolute_url(self):
        return reverse('instagram:post_detail', args=[self.pk])
        
```

```html
<a href="{{ post.get_absolute_url }}">
   {{ post.message }}
</a>
```

- 다음과 같이 모델 클래스 내부에 설정해보자. 그리고나서 html template에서는 간단하게 detail url를 설정할 수 있다. 
- 또한, django shell에서도 확인가능하다.

```terminal
In [1]: from django.shortcuts import resolve_url

In [2]: from instagram.models import Post

In [3]: resolve_url(Post.objects.first())
Out[3]: '/k-instagram/105/'
```

### 그 외 활용
- CreateView / UpdateView
  - success_url을 제공하지 않을 경우, 해당 model instance 의 get_absolute_url 주소로 이동이 가능한지 체크하고, 이동이 가능할 경우 이동
  - 생성/수정하고나서 Detail화면으로 이동하는 것은 자연스러운 시나리오
  - success_url은 저장까지 완료되었을 때, 성공했을 때의 이동할 주소를 의미. 이걸 만약 지정하지 않은 경우에는 오류가 나는데 지정하지 않고도 만약 모델 인스턴스의 get_absolute_url이 있다면 -> 바로 해당 absolute_url이 반환해주는 주소로 이동하게 된다. 즉, detail View로 이동한다.

- 특정 모델에 대한 Detail뷰를 작성할 경우
  - Detail뷰에 대한 URLConf설정을 하자마자, get_absolute_url설정을 하면 코드가 간결해진다.
