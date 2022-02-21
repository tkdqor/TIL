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



### 200번대 응답하는 예시
- 단순히 HttpResponse 응답을 만들어서 return하는 경우 200번대이다. render, JsonResponse도 200이다.

```python
from django.http import HttpResponse, JsonResponse
from django.shortcuts import render

def view1(request):
    return HttpResponse('Hello, Ask Company')
def view2(request):
    return render(request, 'template.html')
def view3(request):
    return JsonResponse({'hello': 'Ask Company'})
```


### 302 응답 예시
- HttpResponseRedirect를 return 하는 경우나 혹은 어떤 이동해야 할 url를 url reverse를 통해서 url를 계산해서 이동하는 형태를 예로 들 수 있다. 하지만 이러한 2가지 경우를 별로 사용하지 않는다. HttpResponseRedirect를 직접적으로 사용하는 경우가 별로 없다.


```python
from django.http import HttpResponseRedirect
from django.shortcuts import redirect, resolve_url

def view1(request):
    return HttpResponseRedirect('/shop/')
    
def view2(request):
    url = resolve_url('shop:item_list') # 후에 배울 URL Reverse 적용
    return HttpResponseRedirect(url)
    
def view3(request):
    # 내부적으로 resolve_url 사용
    # 인자로 지정된 문자열이 url reverse에 실패할 경우,
    # 그 문자열을 그대로 URL로 사용하여, redirect 시도
    return redirect('shop:item_list')
```

- 대신 redirect라는 shortcuts 함수를 통해서 redirect를 처리한다. 내부적으로 HttpResponse 객체를 만들어준다. redirect 괄호안에는 url(redirect('/shop/'))도 쓸 수 있고, 패턴 네임(redirect('shop:item_list'))도 사용할 수 있다. 
  - redirect를 django View에서 할 때는 shortcuts redirect 함수를 사용하자.



### 404 응답 예시
```python
from django.http import Http404, HttpResponseNotFound
from django.shortcuts import get_object_or_404
from shop.models import Item

def view1(request):
    try:
        item = Item.objects.get(pk=100)
    except Item.DoesNotExist:
        raise Http404
    # 생략
    
def view2(request):
    item = get_object_or_404(Item, pk=100) # 내부에서 raise Http404
    # 생략
    
def view3(request):
    try:
        item = Item.objects.get(pk=100)
    except Item.DoesNotExist:
        return HttpResponseNotFound() # 잘 쓰지 않는 방법
    # 생략
```

- View 함수에서 특정 하나의 레코드를 queryset.get이라는 이름의 메소드를 통해서 하나에 접근할 수 있게 되는데, 만약 매칭되는 객체가 0개일 때 / 1개일 때 / 2개 이상일 때 이렇게 각각에 대해서 처리가 달라지게 된다.
  - 0개일 때는 DoesNotExist 예외가 발생
  - 1개일 때는 정상 반환을 해준다.
  - 2개일 때는 multipleobjectsreturned라는 예외가 발생

- 예외가 발생했을 때, try - except로 잡지 않게 되면 -> DoesNotExist 에러가 500번 에러로 처리가 되어버린다. 
  - 하지만, 이 경우는 404번 에러로 처리하는 것이 웹의 개념에 맞기 때문에 위의 코드처럼 Http404라는 예외 클래스를 raise, 발생시켜서 처리를 할 수 있다.
  - 이 처리에 대한 심플한 코드로 get_object_or_404(Item, pk=100) 함수를 활용할 수 있다.
  - 혹은, 예외가 아니라 직접 HttpResponse 객체를 반환하고 싶다면 HttpResponseNotFound() 라는 응답을 return 할 수도 있다. 하지만 잘 사용하지 않는다.



### 500 응답 예시
- 뷰에서 요청 처리 중에, 뷰에서 미처 잡지못한 오류가 발생했을 경우 -> IndexError, KeyError, django.db.models.ObjectDoesNotExist 등 이렇게 다양한 에러가 발생할 수 있다...

```python
from shop.models import Item

def view1(request):
    #	IndexError
    name	=	['Tom',	'Steve'][100]
    
    #	지정 조건의 Item	레코드가 없을 때,	Item.DoesNotExist	예외
    #	지정 조건의 Item	레코드가 2개 이상 있을 때,	Item.MultipleObjectsReturned	예외
    item	=	Item.objects.get(pk=100)
```

- 우리가 예외를 처리할 때에 무작정 try - except에 exception이라고 최상위 예외 클래스를 쓰거나 DoesNotExist 이러한 항목을 생략하고 except: 이렇게만 쓰면 모든 예외를 한 번에 잡게 되는데 그렇게 처리하는 건 안티패턴이다. 무조건 해당 View 함수에서 모든 예외 exception을 잡아서 단순히 처리하기 보다는, 우리가 예상치 못한 예외가 발생했을 때는 500에러가 발생하는 건 당연한 것이다. 
  - 에러가 무조건 나지 않도록 막는 것이 아니라, 관리하는 것이기 때문이다. 500에러가 발생했을 때 적절히 로그를 남겨두면 -> 해당 로그를 빨리 인지해서 해당 버그를 빨리 수정할 수 있다.



### 다양한 HttpResponse 서브 클래스
- 지정 상태코드의 응답이 필요할 때

- HttpResponseRedirect : 상태코드 302
- HttpResponsePermanentRedirect : 상태코드 301 (영구 이동)
- HttpResponseNotModified : 상태코드 304
- HttpResponseBadRequest : 상태코드 400
- HttpResponseNotFound : 상태코드 404
- HttpResponseForbidden : 상태코드 403
- HttpResponseNotAllowed : 상태코드 405
- HttpResponseGone : 상태코드 410
- HttpResponseServerError : 상태코드 500
