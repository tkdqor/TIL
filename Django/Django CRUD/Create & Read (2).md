## CRUD 중, Create
- 우리가 admin 페이지로 데이터를 생성할 수 있었지만, 일반 유저가 보면 안되는 페이지였다. 일반 유저가 웹 서비스에서 글을 생성하면 데이터베이스에 데이터가 추가될 수 있게끔 구성해보자.


### HTML Form 
- 웹 서비스는 일반적으로, 서비스 제공자는 정보를 제공하고 client는 제공받은 정보를 소비하기만 하는데, 로그인이나 회원가입, 검색과 같은 기능들은 서비스 이용자가 본인이 원하는 데이터를 직접 입력해서 서비스 제공자에게 
  해당 데이터를 전달해주게 된다. 사용자가 어떤 데이터를 원하는지, 어떤 계정으로 로그인하고 싶은건지 등 사용자가 어떠한 데이터를 server에게 전달해줘야 server가 그에 맞게끔 처리할 수가 있을 것이다.
  - 이처럼, HTML 페이지에서 사용자가 어떤 값을 입력할 수 있게끔 하는 방법이 있는데, 바로 HTML Form element이다.

### HTML Form 실습
- 먼저, 목록을 보여주는 template으로 가서 링크를 추가해주자.

```html
<body>
    <h1>Posts</h1>
  
  ...
  
    <a href="{% url 'posts:new' %}">글쓰기</a>
</body>
```

- 목록 페이지에서 글쓰기 버튼을 만들고, 해당 버튼을 누르면 posts 앱의 new라는 이름을 가진 urlpattern으로 연결했다. 이제 urls.py로 가서 설정하기.
```python
app_name = 'posts'
urlpatterns = [
    # 전체 게시판 조회
    path('', views.index, name='index'),
    # 게시글 상세 페이지
    path('<int:pk>/', views.detail, name='detail'),
    # 게시글 생성 페이지
    path('new/', views.new, name='new'),
]
```

- posts/new/ 라는 url로 요청되는 순간, views.py에 있는 new 함수를 호출하고 name도 new라고 설정했다. views.py로 가서 new 함수를 새롭게 정의해야 한다.
  - new 함수는 새로운 게시물을 작성하기 위한 template만 rendering하면 되는 것이기 때문에, 별도의 데이터 조회가 필요하지 않다. 
```python
# 게시판 생성
def new(request):
    return render(request, 'posts/new.html')   
```

- 새로운 게시물을 작성하기 위한 new.html 페이지를 설정했다. 이제 글을 작성할 수 있도록 new.html 파일을 생성하자.
  - new.html에서 form element를 생성하고, 그 안쪽에 input element를 생성하면 type이라는 attribute를 볼 수 있다. 이 type를 수정하여 다양한 종류의 input element를 만들 수 있다. 기본적으로
    뭔가를 입력할 수 있는 element이다. 
```python
<body>
    <h1>New Post</h1>

    <form action="">
        <input type="text">
    </form>
   
    <a href="{% url 'posts:index' %}">목록</a>
</body>
```

- 추가로, 이 input element에 대한 설명을 해줄 수 있다. input element를 설명하기 위한 용도로 label element라는 것이 있다.
- label element에서는 for attribute를 사용해서 정확하게 어떤 input element를 위한 label인지 지정할 수 있다. 또한, 연결시키고자 하는 input element에다가 id attribute를 부여해줘야 한다.
```python
 <form action="">
        <label for="author">Author</label>
        <input id="author" type="text">
  </form>
```    

- 이렇게 label for attribute에는 input의 id를 입력해주면, 아래와 같이 input element에 어떤 정보를 입력하면 되는지 표시할 수 있다. 
  - 그리고, label과 input이 연결되어있기 때문에 label를 클릭하면 -> input elemenet에 포커스가 잡히게 된다.
<img width="212" alt="image" src="https://user-images.githubusercontent.com/95380638/154027480-cc54351c-46e6-411a-a834-e90116a00c63.png">


### 뭔가를 입력할 수 있게 해주는 element는 input, 그리고 textarea element가 있다.
- input element와 다르게, 여러 줄에 걸쳐서 장문을 작성할 수 있는 element이다. 
  - textarea의 cols와 rows attribute는 -> 입력창의 너비와 높이를 의미한다. 그리고 id attribute를 사용하면 textarea가 어떤것을 입력하는 것인지 label과 연동시켜 줄 수 있다.
  - textarea는 장문의 본문을 작성할 것으로 id를 body라고 지정하고, label의 for에도 body라고 지정해보자.

```python
<form action="">
        <div>
            <label for="author">Author</label>
            <input id="author" type="text">
        </div>
        
        <div>
            <label for="body">Body</label>
            <textarea id="body" cols="40" rows="10"></textarea>
        </div>
</form>
```    
<img width="402" alt="image" src="https://user-images.githubusercontent.com/95380638/154031717-abd527a9-8dd1-4118-ae4a-96df513a55a0.png">

- 위와같이 코드를 입력하면, body라는 label을 클릭할 때 textarea가 잡히는 것을 확인할 수 있다.

- label과 input element, 그리고 textarea element 전부 다 inline element라서 한 줄로 늘어지게 된다.
   - block element를 사용해서 섹션을 구분해주자. 관련있는 것들끼리 div element로 구분하기.


### Form element에 작성한 데이터를 server에 보내기
- form element 하단에 input element를 만들되, type를 text가 아니라 submit으로 변경해주자. -> 그러면 웹 브라우저 화면에 "제출" 이라는 이름의 버튼이 생성된다. 이건 defalut 값이라고 보면 되고,
  - input element의 value라는 attribute를 사용하면, 다른 이름으로 바꿔줄 수 있다.

- 우리가 지금까지 만든 form에다가 author를 입력하고 body에 텍스트를 넣어 입력한 다음 버튼을 누를 때 -> 우리가 입력한 데이터를 바탕으로 새로운 게시물 생성을 server에게 요청해야 한다.
  - **즉, 데이터를 담아 새로운 Http Request를 전송해야 된다는 것이다.**
  - **그래서 Form element에서 submit 기능을 사용하게 되면 -> Http Request가 새롭게 발생한다.**     
    **또한, Http Request를 어떻게 발생시키고 어떠한 HTTP Method를 활용해서, 어떤 HTTP urlpattern으로 그 request를 전송할 것인지를 -> form element의 action attribute와 method attribute로 지정할 수 있다.**
    - form element의 method attribute는 내가 어떠한 HTTP method를 사용할 것인지 지정하면 된다. ex) GET 또는 POST
    - form element의 action attribute는 어떤 url pattern으로 request를 전송할 것인지를 결정해준다. url를 적어주면 된다. -> 아무것도 적지 않으면, 현재 페이지의 url 주소로 request를 다시 전송하게 된다.

- 데이터를 입력하고 버튼을 눌러보면, 현재 페이지로 HTTP Request를 전송하고, 주소창에는 
<img width="344" alt="image" src="https://user-images.githubusercontent.com/95380638/154036162-8c004fa3-c38d-4ecf-ad7a-5c919b46211f.png">

- 다음과 같이 물음표가 생긴다. 페이지가 완전히 새로고침 된 것이기 때문에 입력한 데이터들은 사라지는 것이다.

```python
<form method="GET" action="">
        <div>
            <label for="author">Author</label>
            <input id="author" type="text" name="author">
        </div>
        
        <div>
            <label for="body">Body</label>
            <textarea id="body" cols="40" rows="10" name="body"></textarea>
        </div>

        <input type="submit" value="작성하기">   
</form>
```

- 위와 같이 input element와 textarea element에 name이라는 attribute를 추가하고 값을 넣어주자. 그리고 이 상태에서 해당 데이터들을 입력하고 버튼을 누르면
<img width="542" alt="image" src="https://user-images.githubusercontent.com/95380638/154037338-2da344aa-8f9c-404f-a810-32620e789432.png">

- 아까와는 다르게, 주소창을 보면 url pattern은 /posts/new/로 똑같지만 ? 뒤에 데이터가 추가되었다.
  - 우리가 form element를 사용할 때에는 사용자가 데이터를 입력하게끔 하고 그렇게 입력한 데이터를 submit, 제출하게 되는데 -> 제출할 때 GET방식이나 POST방식으로 우리가 원하는 url pattern으로 HTTP Request를 전송하게 된다.
  - name attribute를 사용하지 않는다면 각 입력값이 어떤것을 위한 값인지 알 수 없지만, name attribute를 사용하면 각 입력값이 어떤것을 위한 입력값인지 구분해줄 수 있게 된다. 
  - 그래서 form element로 input이나 textarea element 처럼 사용자가 값을 입력할 수 있는 element를 활용할 때는 -> 사용자가 입력한 값이 HTTP Request를 통해 server에 전달될 때, 각각의 값들이 무엇에 대한 값인지를 표현하기 위해서 name attribute로 값에 대한 이름을 지정하게 된다.
    - 만약, HTTP Request가 GET 방식으로 데이터를 전송하게 된다면 -> url pattern 다음에 ? 뒤에다가 데이터의 name=그 name에 해당하는 value가 주소창에 표시된다. 이미지에서는 author라는 name을 가진 데이터의 value로 보라돌이라는 데이터가 전달된 것이다. 전달하고자 하는 데이터가 여러 개라면 &를 사용하여 여러 개의 데이터를 구분해서 주소창에 나열하게 된다.
    - ex) 구글 검색창이나 네이버 검색창을 보면, input element에다가 우리가 검색하고자 하는 값을 입력하면 -> 주소창에 www.google.com/search 라는 새로운 url pattern으로 GET방식을 활용해 HTTP Request가 전송된 것이다. /search 라는 url 뒤에 ?가 붙으면서 q=강아지 이렇게 input element의 name이 q이고 거기에 우리가 입력한 강아지가 데이터로 담겨 전송된 것을 알 수 있다.
    - **결론적으로, GET 방식으로 데이터를 전송하게 되면 url pattern이 바뀌면서 url pattern 뒤에 ?가 붙게되고, 그 뒤에 전송하고자 하는 데이터들이 name=value의 형태로 나열이 되며, 여러 개를 나열할 때는 & 기호를 사용해서 구분해준다는 것이다.**
    - ? 뒤에 붙는 GET 방식으로 전달하고자 하는 데이터를 우리는 GET 파라미터, url 파라미터, query string이라고 한다.


### 우리가 GET 방식으로 데이터를 전송하는 이유
- 우리가 GET 방식으로 데이터를 전송하는 이유는, 사용자가 입력한 데이터가 주소 자체에 기록되게끔 할 수 있고 이 기록되어 있는 데이터를 활용해서 웹 페이지 자체에 보여주거나, 웹 페이지를 키워드와 관련된 데이터로 구성하기 위해 활용한다. 주소 자체에 우리가 전송한 데이터가 기록되어 있기 때문에 이 주소를 하나의 링크 형태로 타인에게 공유하면 우리가 검색한 결과가 동일한 결과를 볼 수 있다.
- 만약, 로그인 화면에서 GET 방식으로 데이터를 전송하게 된다면 주소창에 아이디 정보와 비밀번호가 그대로 노출될 것이다.
- 그리고 게시물 작성 역시 이렇게 노출되는 것이 문제가 될 수 있다.


### Form element에 작성한 데이터를 server에 보내기 (2)
```html
<form method="GET" action="/posts/create">
...
</form>
```

- 이렇게 action에 특정한 url을 지정하고 다시 값을 입력해서 버튼을 누르면, /posts/create?author=보라돌이&body=도리도리 -> 이렇게 url pattern이 바뀌고, /posts/create라는 패턴으로 GET 방식을 활용해 HTTP Request가 전송된 것이다. 또한, 전송이 될 때 author=보라돌이 와 body=도리도리 라는 데이터가 함께 전송이 된 것이다.
- 이 /posts/create 라는 url pattern를 urls.py에 설정해보자.

```python
app_name = 'posts'
urlpatterns = [
    # 전체 게시판 조회
    path('', views.index, name='index'),
    # 게시글 상세 페이지
    path('<int:pk>/', views.detail, name='detail'),
    # 게시글 생성 페이지
    path('new/', views.new, name='new'),
    # 게시글 생성 HTTP Request
    path('create/', views.create, name='create'),
]
```

- 이렇게 /posts/create/ 를 처리해줄 수 있게끔 path 함수를 설정했다. 이제 views.py에서 create라는 함수를 새롭게 정의하자.
```python
# 게시글 생성 기능
def create(request):
    print(request.GET)
    
    return render(request, 'posts/create.html')
```

- 먼저 이렇게만 작성해보고 create 함수를 실행시켜보면, server 로그로 <QueryDict: {'author': ['보라돌이'], 'body': ['도리도리']}> 이렇게 출력이 된다.
  - QueryDict란, 딕셔너리와 사용하는 방식이 똑같다.
  - **모든 View 함수의 첫번째 파라미터로 request를 작성하는데, 이 View 함수가 실제로 호출될 때는 -> 이 request라는 파라미터에 HTTP Request에 대한 모든 정보가 담겨져 있다.**
  - **request.GET 이렇게 사용하면 -> GET 방식으로 request가 전송된 데이터를 QueryDict 형태로 전부 다 확인할 수 있는 것이다.** 그래서 여기서 우리가 원하는 데이터를 뽑아 쓸 수 있다.
    - **QueryDict에서 데이터를 추출할 때도, request.GET['author'] 이런식으로 key값으로 접근하면 된다. 그래서 전송된 데이터를 추출할 수가 있다.**
    - 그리고 여기서 author라는 key와 body라는 key는 -> 우리가 new.html의 input, textarea element에 name attribute로 지정해주었던 value인 것이다.

- **딕셔너리에서 데이터를 접근할 때, 해당 key가 없을 때도 접근을 시도하면 에러가 발생할 수가 있다.** 
  - 그래서 조금 더 안전하게 하려면, 딕셔너리에서의 .get() 함수를 사용하면 된다. .get() 함수를 사용했을 때 없는 key로 접근하면 None이 출력된다.
  - request.GET.get('author') , request.GET.get('body') 이렇게 추출하게 되면 -> 보라돌이 , 도리도리 이러한 데이터를 추출할 수 있게 된다.
  - 실제로 이렇게 추출한 데이터를 데이터베이스에 생성시키기 전, 데이터를 활용만 해본다면,

```python
# 게시글 생성 기능
def create(request):
    print(request.GET)
    author = request.GET.get('author')       # request.GET / 여기까지 딕셔너리의 형태이고 .get() 함수를 사용해서 안전하게 접근
    body = request.GET.get('body')

    context = {
        'author': author,
        'body': body,
    }

    return render(request, 'posts/create.html', context)
```

- 이렇게 create 함수에서 GET 방식의 HTTP Request로 전송된 데이터를 author, body라는 변수로 저장하고 그것들을 create.html에 보낼 수 있다.

```html
<body>
    <h1>Result</h1>

    <h2>{{ author }}</h2>
    <h3>{{ body }}</h3>
    
   
    <a href="{% url 'posts:index' %}">목록</a>
</body>
```

- create.html에서는 이렇게 context로 전달받은 데이터를 출력해줄 수 있다.
