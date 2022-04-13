## Function Based View 작성하기 (2)
- View에서 데이터를 받을 때, 터미널에서 조금 더 직관적으로 확인하는 방법이 있다. print할 때 f string를 사용하는 것이다.

```python
def url_parameter_view(request, username):
    print('url_parameter_view()')
    print(f'username: {username}')
    print(f'request.GET: {request.GET}')
    return HttpResponse(username)
```

- 이렇게 하면 터미널에 

```terminal
username: projectlion
```

- 이런식으로 확인할 수 있게 된다.
- **또한, urls.py에서 꼭 문자열이 아니라 숫자를 받을 수도 있다.**

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('url/', url_view),
    path('url/<int:username>/', url_parameter_view),
]
```

- 이렇게 path에서 int로 정의했을 때는 문자열을 입력하면 에러가 난다. 그래서 url에 숫자를 입력하면 정상적으로 출력된다.


### HTTP method 관련
- **그전에, 1가지 좋은 팁 --> VSCode에서 HttpResponse나 render를 마우스로 갖다대면 --> 클래스면 (class), 함수이면 (function) 이렇게 표시를 해주고 어떻게 사용해야 하는지도 알려준다.**
  - **그리고 아예 command를 누르고 클릭하면 해당 클래스나 함수가 위치한 곳으로 이동하기 때문에 더 자세하게 볼 수 있다.**

- ex) render 함수를 커맨드로 클릭해보면,

```python
def render(
    request, template_name, context=None, content_type=None, status=None, using=None
):
    """
    Return an HttpResponse whose content is filled with the result of calling
    django.template.loader.render_to_string() with the passed arguments.
    """
    content = loader.render_to_string(template_name, context, request, using=using)
    return HttpResponse(content, content_type, status)
```

- **이렇게 정의되어있다. 여기서 context=None, content_type=None, status=None, using=None --> 이렇게 None이라고 되어있는 부분은 디폴트값으로 None이 들어가 있다는 것을 의미한다. 
  즉, 우리가 이 함수를 사용할 때 해당 값을 넣지 않아도 된다.**
  

- 다시 돌아와서, 우리의 View에서는

```python
def function_view(request):
    return render(request, 'view.html')
```

- 이렇게 render 다음에 request, template_name를 넣어주면 된다. 
