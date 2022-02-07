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
    
    # instagram/templates/instagram/post_list.html
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

* 지금은 함수 기반의 view이지만, class 기반 view를 사용할 때 참조할 수 있게끔 함수의 이름이나 딕셔너리 key 값을 설정해줘야 한다.


**3) instagram 앱 내부에 templates 디렉터리 -> instagram 디렉터리 -> 해당 경로에 post_list.html 생성하기**
```python
{{ post_list }}
```

- view 함수에서 딕셔너리로 전달한 key 값을 html 파일에서 변수로 사용할 수 있기 때문에, html에서 post_list를 활용하자.


**4) instagarm 앱 내부의 urls.py 설정**
```python
from django.urls import path
from . import views


urlpatterns = [
    path('', views.post_list)
]
```

- 같은 디렉터리 내부에 있는 views.py를 import 해주기. 그리고 path 함수를 사용하기 위해 django 디렉터리 -> urls 디렉터리로부터 import 하기 
- path 함수를 사용해서 localhost:8000/instagram/ 일 경우에는 -> view 함수 중 post_list로 연결해주기.
  - **views.post_list() 이렇게 함수를 호출하는 것이 아니라, views.post_list 이렇게 함수 그 자체를 넣어준다는 점 유의하자.**


**5) 이제 localhost:8000/instagram/ 으로 확인**
- 해당 주소로 웹 브라우저에서 확인해보면, <QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]> 이렇게 화면에 뜨게 된다.
- post_list view 함수에서 q가 있을 경우에만 qs가 filter를 하게끔 설정했으니, 지금은 q가 없어서 qs 변수가 Post 모델의 모든 데이터를 담고 있다. 따라서 html에서도 Post 모델의 모든 데이터를 보여주게 된다.

- **추가로, html template을 만들 때는 html이 갖춰야 할 최소한의 포맷이 있다.**
```html
<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8" />
    <title>Instagram / Post List</title>
</head>
<body>
    {{ post_list }}    
</body>
</html>
```

- html도 스펙이 있다. 버전이 있는데, <!doctype html> 이 코드는 html5를 사용하겠다는 것이다.
  - html lang="ko" 는, 어떤 언어로 작성하는지 설정. 검색엔진이나 시각장애자들을 위한 앱들이 편하게 사용할 수 있게 한다.
  - meta charset="utf-8" 이 코드로 인코딩을 지정. 그래서 해당 페이지가 utf-8 인코딩으로 작성되어 있다는 것이다.


**6) django template language**
- django template에서는, python 문법을 사용할 수 없다. 대신, django template만의 template 문법이 있다. 
```html
<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8" />
    <title>Instagram / Post List</title>
    <!-- getbootstrap  -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
</head>
<body>
    <table class="table table-bordered table-hover">
        <tbody>
            {% for post in post_list %}
                <tr>
                   <td>
                        {% if post.photo %}
                            {{ post.photo.url }}
                        {% else %}
                            No Photo     
                        {% endif %}
                    </td>
                    <td>
                        {{ post.message }}
                    </td>
                </tr>
            {% endfor %}    
        </tbody>    
    </table>
</body>
</html>

```

- 다음과 같이 python template 엔진 문법을 사용할 수 있다.
- post_list에서 post를 하나씩 꺼내서 Post 모델에 있는 필드에 접근할 수 있다. ex) post.message / post.photo

- html 디자인을 위해 table element를 사용해보자. 그 안에는 tbody element로 감싸고 tr element는 하나의 행을 뜻한다. 그리고 td element는 한 칸을 의미한다.

- 이미지를 위해 {{ post.photo.url }} -> 이렇게 설정하면 The 'photo' attribute has no file associated with it. 이러한 에러가 뜨게 된다. 이 에러는 사진이 들어있지 않은 post도 있기 때문에 발생하게 된다. 그래서 사진이 있을 경우에만 사진을 보여줄 수 있도록 {% if post.photo %} 로 시작해서 {% else %} / {% endif %}로 설정하면 된다. 그러면 아래처럼 url이 보이게 된다.

<img src="https://user-images.githubusercontent.com/95380638/152721077-740257e2-5efc-424c-9de9-c9de27c2e307.png" width="50%" height="50%">




**7) getBootstrap 사용해보기**
- https://getbootstrap.com/ 에 들어가서, CSS 관련 링크를 가져와서 html 파일 head element에 추가해보자. 그 상태에서 새로고침해도 스타일이 적용된 걸 볼 수 있다.
- 그리고 이제 table element에 class만 table table-bordered table-hover로 설정해줘도 스타일이 변한다.
