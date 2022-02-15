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

