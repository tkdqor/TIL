## 적절한 HTTP 상태코드로 응답하기


### HTTP 상태코드
- 웹서버는 적절한 상태코드로서 응답해야 한다. 
- 각 HttpResponse 클래스마다 고유한 status_code가 할당된다. https://github.com/django/django/blob/main/django/http/response.py
  - django View에서 응답, 인스턴스에 대한 타입은 HttpResponse 타입이다. HttpResponse는 상태코드를 가지는데 그 코드는 default가 200이다. 즉, OK라는 의미이다.

- REST API에서는 Http 상태코드를 적절히 활용하게 된다.

```python
from django.http import HttpResponse

def test_view(request):
    # Return a "created" (201) response code.
    return HttpResponse(status=201)
```

- 상태코드를 정의하는 방법으로는 해당 view 함수에서 HttpResponse 인스턴스를 만들 때, status라는 이름으로 상태코드를 지정할 수 있다. 

```
# django/http/response.py
class HttpResponseRedirect(HttpResponseRedirectBase):
    status_code = 302
```

- 혹은, HttpResponse를 상속받은 위의 클래스 기반 View에서 status_code라는 이름으로 우리가 상태코드를 지정할 수 있다.


### 대표적인 상태코드
- 200번대 : 성공 
  - 200 : 서버가 요청을 잘 처리했다. -> OK
  - 201 : 작성됨. 서버가 요청을 접수하고, 새 리소스를 작성했다. ex) 새로운 게시글을 생성하거나 새로운 댓글이 생성되거나 등등

- 300번대 : 요청을 마치기 위해, 추가 조치가 필요하다.
  - 301 : 영구 이동, 요청한 페이지가 새 위치로 영구적으로 이동했다. ex) 검색엔진에서 해당 원출처에 대해서 이동할 주소로 영구 이동을 했으니 검색엔진 내부에서 url을 수정하는 작업을 수행할 것이다.
  - 302 : 임시 이동, 페이지가 현재 다른 위치에서 요청에 응답하고 있지만, 요청자는 향후 원래 위치를 계속 사용해야 한다.

- 400번대 : 클라이언트측 오류
  - 400 : 잘못된 요청.  ex) 요청이 왔을 때 어떠한 항목이 누락, 유효성 검사에 대한 실패가 발생했을 경우.
  - 401 : 권한없음  ex) 유저가 어떠한 View에 접근하고자 할 때, 인증이 되어있지 않을 경우, 인증이 필요할 때 사용됨.
  - 403 (Forbidden) : 필요한 권한을 가지고 있지 않아서, 요청을 거부  ex) 403은 유저가 인증을 했지만, 인증 이후에 해당 View나 해당 url에 접근을 하기 위한 권한이 없는 경우
  - 404 : 서버에서 요청한 리소스를 찾을 수 없다.  ex) 서버에서 해당 레코드가 없을 경우
  - 405 : 허용되지 않는 방법. POST 방식만을 지원하는 뷰에 GET요청을 할 경우

- 500번대 : 서버측 오류
  - 500 : 서버 내부 오류 발생  ex) django에서는 해당 View 함수에서 어떤 예외를 처리하지 못하고 임의의 오류가 발생했을 경우 500번대 응답을 발생시키게 된다. 
    
