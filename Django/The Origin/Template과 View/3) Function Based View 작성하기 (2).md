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
    print(f'request.method: {request.method}')
    print(f'request.GET: {request.GET}')
    print(f'request.POST: {request.POST}')
    return render(request, 'view.html')
```

- **이렇게 render 다음에 request, template_name를 넣어주면 된다. 그리고 파악을 해보기 위해 request.method / request.GET / request.POST 를 print로 찍어보자.**
  - **View에서 데이터를 받는 방법이 1) 경로에 변수를 지정하는 방법 / 2) ?를 사용해서 쿼리 스트링으로 key,value형식으로 받는 방법 / 3) 마지막으로 html form을 이용해서 POST방식으로 데이터를 쏠 수 있다.**

- 이 상태에서 url pattern를 하나 더 추가해보자.

```python
from posts.views import url_view, url_parameter_view, function_view

urlpatterns = [
    path('admin/', admin.site.urls),
    path('url/', url_view),
    path('url/<str:username>/', url_parameter_view),
    path('fbv/', function_view),
]
```

- path('fbv/', function_view) 이렇게 해서 위에서 만든 함수를 연결해주자.

- **그 다음으로는 새로운 template를 만들어준다. posts App 내부에 templates 라는 디렉터리를 만들고 -> view.html 파일을 만들어준다.**
  - 그리고 VSCode에서 html에서 !를 누르고 자동완성 되는 것을 눌러주면 기본적인 구조의 html 코드를 완성시켜준다.
  - 이렇게 해주면 url에서 fbv/를 입력했을 때 빈 화면이 출력된다. 

- view.html에 우리가 데이터를 받을 수 있도록 form element를 입력해보자.

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
    <a href="fbv/">새로고침</a><br>

    <!-- GET 방식으로 보내기 -->
    <form action="" method="GET">
        <input type="text" name="var">
        <input type="submit" value="GET 제출">
    </form>

    <!-- POST 방식으로 보내기 -->
    <form action="" method="POST">
        {% csrf_token %}
        <input type="text" name="var">
        <input type="submit" value="POST 제출">
    </form>
</body>
</html>
```

- 새로고침 a element 하나 만들고, 2개의 form를 사용해서 하나는 GET / 하나는 POST 방식으로 설정하자.
  - **새로고침 a element에서 href를 href="fbv/" 이게 아니라 --> href="/fbv/" 이렇게 앞에 슬래쉬를 꼭 붙여줘야 한다. 슬래쉬가 없이 시작하게 되면, 현재 경로에서 들어가는 것이기 때문에 되지 않는다.**
- **POST방식일 때는, {% csrf_token %} 이렇게 토큰을 넣어줘야 한다.**

<img width="294" alt="image" src="https://user-images.githubusercontent.com/95380638/163130778-1a4a2542-078d-411e-bae2-51c688b0fecc.png">

- 그러면 다음과 같이 데이터를 받을 수 있게 된다.



