## views.py에서 QuerySet를 사용하여 로직 구성 
- views.py에 설정하는 함수는 첫번째 파라미터로 request를 설정 - 이 request라는 객체에 요청과 관련된 모든 내역들이 담긴채로 전달된다.
  - request의 GET / POST 인자가 있으며, request.GET() / request.POST() 이렇게 사용한다. ex) q = request.GET.get('q', '') -> GET 인자에서 q라는 이름의 인자를 가져오는데, 만약 없다면 default로 빈 문자열을 반환하라는 것이다.
- 템플릿 시스템을 활용하는 함수가 바로 render() 함수이다.


## views.py에서 QuerySet를 사용하여 간단 검색 구현
**1) 먼저 프로젝트 디렉터리 내부 urls.py에서 blog1 / instagram 앱 별로 include를 사용하여 url를 구분지어주기.**

**2) instagram 앱 -> views.py 설정**
```python
from django.shortcuts import render
from .models import Post

def post_list(request):
    qs = Post.objects.all()
    q = request.GET.get('q', '')
    if q:
        qs = qs.filter(message__icontains=q)

    return render(request, 'instagram/post_list.html', {
        'post_list': qs,
    })    
```


- post_list라는 함수를 설정하면서 파라미터로 request -> 호출 당시에 현재 요청에 대한 모든 내역을 인자로 전달 받으려고 한다는 의미.
  - 함수 내부에서 request.GET / request.POST / request.FILES  이렇게 사용할 수 있다. GET / POST / FILES 인자로부터 우리가 꺼내쓸 수 있다.
  - q = request.GET.get('q', '') 이렇게 get 함수로 q라는 이름의 인자가 있으면 가져오고, 원래 없으면 None을 반환하는데 두번째 인자로 빈 문자열을 받겠다고 설정할 수 있다. 즉, client가 뭔가를 검색한 게 q라는 변수에 담겨지게 된다.

-  qs = Post.objects.all() -> 해당 queryset으로 Post 모델 데이터의 전체를 가져올 준비를 해주자.

- q라는 검색어가 있다면, Post 모델 전체를 조회한 qs에서 filter 함수를 사용하여 -> 대소문자 구분하지 않고 q를 찾아서 filter를 해준다.

- def post_list 라는 함수의 return 값으로, 우리는 render 함수를 통해 HTML 응답을 해줄 수 있다.
  - render의 첫번째 인자는 view 함수의 request 인자를 그대로 넘겨주고, 두번째 인자는 '앱이름/원하는 템플릿명' 을 입력. 세번째 인자는 딕셔너리로 템플릿 내에서 우리가 위에서 정의한 qs라는 변수를 value로 설정. 그리고 참조할 이름인 key값도 설정.
