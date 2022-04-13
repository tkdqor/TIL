## Function Based View
- liongram 프로젝트 디렉터리를 열어주자.
- 지금 내가 사용하고 있는 python interpreter가 어디에 있는지 확인하려면 터미널에

```terminal
python

>>> import sys
>>> sys.executable
'/Users/kimsangbaek/Desktop/Mission/Challenge/first-django/venv/bin/python'

```

- 이렇게 python 실행시키고 sys를 import한 다음, executable 속성을 입력해보자. 그러면 지금 선택된 인터프리터의 경로가 나오게 된다.

- 인터프리터 설정은, VSCode에서 View -> Command Palette -> interpreter를 입력하면 나오는 버튼 클릭 -> Find -> 현재 프로젝트 내부에 있는 venv -> 그 다음 bin 내부에 있는 python를 선택해주면 된다.


### 실습 진행
- **먼저 urls.py로 가서 설정하기**

```python
from django.contrib import admin
from django.urls import path
from posts.views import url_view

urlpatterns = [
    path('admin/', admin.site.urls),
    path('url/', url_view),
]
```
- 이렇게 url_view를 import 해서 설정하기.

- **그리고 posts 앱 내부에 view도 설정해주기**

```python
from django.http import HttpResponse

def url_view(request):
    return HttpResponse('url_view')
```

- 그 다음, VSCode 위쪽에 Run -> Run Without Debugging를 클릭해줘도 서버가 실행된다. Liongram 프로젝트 디렉터리 내부 .vscode -> launch.json이 있어야 실행이 된다.

* * *
- 지금까지 View는 그냥 텍스트 자체를 응답한 것이다. 그런데,

```python
def url_view(request):
    return HttpResponse('<h1>url_view</h1>')
```

- **이렇게 태그를 넣어도 작동이 된다. 왜 Html로 작동이 되냐면, HttpResponse라는 클래스를 command로 들어가보면**

```python
class HttpResponse(HttpResponseBase):
    """
    An HTTP response class with a string as content.

    This content can be read, appended to, or replaced.
    """

    streaming = False

    def __init__(self, content=b"", *args, **kwargs):
        super().__init__(*args, **kwargs)
        # Content is a bytestring. See the `content` property methods.
        self.content = content
    ...
```

- **우리가 인자로 넣었던 부분이 던더 init의 content가 된다. 그리고 HttpResponse는 HttpResponseBase라는 클래스를 상속받았다. 이 Base를 들어가보면**

```python
class HttpResponseBase:
    """
    An HTTP response base class with dictionary-accessed headers.

    This class doesn't handle content. It should not be used directly.
    Use the HttpResponse and StreamingHttpResponse subclasses instead.
    """

    status_code = 200

    def __init__(
        self, content_type=None, status=None, reason=None, charset=None, headers=None
    ):
        self.headers = ResponseHeaders(headers or {})
        self._charset = charset
        if content_type and "Content-Type" in self.headers:
            raise ValueError(
                "'headers' must not contain 'Content-Type' when the "
                "'content_type' parameter is provided."
            )
        if "Content-Type" not in self.headers:
            if content_type is None:
                content_type = "text/html; charset=%s" % self.charset
            self.headers["Content-Type"] = content_type
        self._resource_closers = []
        # This parameter is set by the handler. It's necessary to preserve the
        # historical behavior of request_finished.
        self._handler_class = None
        self.cookies = SimpleCookie()
        self.closed = False
        ...
```

- **중간에 보면, Content-Type이 없으면 -> text/html 파일을 주게 되어있다. 그러니까 우리가 h1 태그를 입력해서 날려주면 텍스트/html로 날라가기 때문에 Html로 랜더링이 되는 것이다.**


* * *
- views에서 추가로 JsonResponse도 사용해보자.

```python
from django.shortcuts import render
from django.http import HttpResponse, JsonResponse

# Create your views here.

def url_view(request):
    data = {'code': '001', 'msg': 'OK'}
    # return HttpResponse('<h1>url_view</h1>')
    return JsonResponse(data)
```    
    
- **JsonResponse의 경우에는 우리가 딕셔너리 형태의 값을 넣어줘야 한다. JsonResponse도 command로 클릭해보면, data가 딕셔너리 형태가 아니면 에러가 나게 되어있다. 그래서 우리가 data라는 변수를 활용해서 딕셔너리를 만들어서 넣어주자.**
- 이렇게 해서 응답을 해주면, 

```html
{"code": "001", "msg": "OK"}
```

- 이런식으로 JSON 형태로 바뀌는 걸 볼 수 있다. 페이지 소스 보기를 우클릭해서 봐도 변함이 없다.(그전에 HttpResponse로 응답했을 때는 html 코드가 보인다)


### View에서 데이터 받기
- View에서 데이터를 받을 때, 여러가지 경우가 있겠지만 url로 받거나, form으로 받거나, Http method로 봤을 때 GET도 있고 POST도 있다. 그래서 View에서 데이터를 받는 방법들을 알아보자.
- 먼저 urls.py에 새로운 url를 추가해보자.

```python
from django.contrib import admin
from django.urls import path
from posts.views import url_view, url_parameter_view

urlpatterns = [
    path('admin/', admin.site.urls),
    path('url/', url_view),
    path('url/<str:username>/', url_parameter_view),
]
```

- **이렇게 url pattern에 변수를 만들 수 있다. 그리고 이렇게 url로 변수를 보내주면 View에서 어떻게 해야 사용할 수 있는지 알아보자.**


- view에 새로운 함수를 추가한다.

```python
def url_parameter_view(request, username):

    return HttpResponse()
```

- url에서 ㅇ


    
    
    
