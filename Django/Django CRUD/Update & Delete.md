## CRUD 중 Update
- 먼저 수정하기 위한 form element를 포함하고 있는 template를 보여주기 위해 url pattern이 하나 필요하고, 
- 그 template에서 우리가 내용을 수정한 다음, HTTP Request 전송을 했을 때 실제 수정이 되고, 수정이 끝난 후 redirect 해줄 수 있는 url pattern이 하나 더 필요하다.

- 그래서, 앱 내부 urls.py에서 url pattern은 추가해준다. 
  - posts/<int:pk>/ 여기까지는 상세 페이지를 의미하는 것이니까 posts/<int:pk>/edit을 생성해서 -> 해당 게시물을 수정한다는 의미를 담는 url pattern를 설정하자.

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
    # 게시글 수정 페이지
    path('<int:pk>/edit/', views.edit, name='edit'),
]
```

- 이제 views.py로 가서 edit 함수를 설정하자.
```python
# 게시글 수정 페이지
def edit(request, pk):
    post = Post.objects.get(id=pk)

    context = {
        'post': post,
    }

    return render(request, 'posts/edit.html', context)
```    
    
- 게시물을 수정하기 위해서는, 먼저 게시물 데이터를 조회해서 어떤 게시물을 수정할지 지정해줘야 한다. 그래서 Post.objects.get() 함수를 통해 게시물을 조회하고, 조회한 게시물을 context로 담아서 template에
  전달해주면 된다. 
  - template은 수정을 할 수 있는 별도의 edit.html로 설정했다.

- 그래서, 앱 내부 templates -> posts 디렉터리 안에 edit.html을 새로 생성하자.

```html
<body>
    <h1>Edit Post</h1>

    <form method="POST" action="{% url 'posts:update' post.id %}">
        {% csrf_token %}

        <div>
            <label for="author">Author</label>
            <input id="author" type="text" name="author" value="{{ post.author }}"> 
        </div>
        
        <div>
            <label for="body">Body</label>
            <textarea id="body" cols="40" rows="10" name="body">{{ post.body }}</textarea>
        </div>

        <input type="submit" value="수정하기">   
    </form>
   
    <a href="{% url 'posts:index' %}">목록</a>
</body>
```

- input element의 value attribute를 활용해서, 수정 페이지를 처음 띄울때부터 input element의 값이 채워져 있는 상태가 될 수 있게끔 하자.
- textarea element의 경우에는 start tag와 end tag 사이에 내용을 작성해주면, 수정 페이지가 뜰 때 부터 값이 채워지게 된다.
- 그리고 form element를 통해 author와 body 내용이 수정되었을 때, 수정된 데이터가 전송될 url를 정해줘야 한다. 실제로 수정을 하기 위한 url pattern을 udpate라는 이름을 가진 pattern으로 설정하자. 
  그런데, 이 update라는 name을 가진 url pattern은 수정할 post 객체의 pk, id 값을 전달해줘야 하기 때문에 -> {% url 'posts:update' post.id %} 다음과 같이 작성해주자.
  
- 이제 다시 urls.py로 가서 update라는 name을 가진 url pattern를 추가하자.

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
    # 게시글 수정 페이지
    path('<int:pk>/edit/', views.edit, name='edit'),
    # 게시글 수정 HTTP Request
    path('<int:pk>/update/', views.update, name='update'),
]
```

- 새로 추가한 update pattern은 반드시 앞에 정수 변수를 받는 url이 되어야 한다. 게시글 하나에 대한 수정이기 때문이다.

- 이제 views.py로 가서 update 함수를 정의하자.

```python
# 게시글 수정 기능
def update(request, pk):
    post = Post.objects.get(id=pk)              # 수정해야할 post 객체 조회
    post.author = request.POST.get('author')    # post.author 값에 사용자로부터 POST 방식으로 입력받은 값으로 수정
    post.body = request.POST.get('body')        # post.body 값에 사용자로부터 POST 방식으로 입력받은 값으로 수정
    post.save()

    return redirect('posts:detail', pk=post.id) # redirect 함수로 게시물 상세 페이지 보여주기
```    

- 먼저 수정하고자 하는 게시물을 조회한 다음에, post.author, post.body의 값을 전달받은 데이터로 수정해주면 된다. request.POST.get('author') 이런식으로 POST 방식으로 전달받은 데이터를 key로 
  접근한 값을 데이터베이스의 값으로 수정해주는 것이다.
- 다 수정한 다음에는 post.save()를 해줘야 데이터베이스에 반영이 된다.
- 마지막에는 redirect 함수를 이용하여 상세 페이지를 보여주는 것으로 설정했다.

- 그리고 상세 페이지에서 수정할 수 있는 페이지인 edit.html로 갈 수 있는 버튼을 생성하자.
```html
<body>
    <h1>Post detail</h1>
  ...
  
    <a href="{% url 'posts:index' %}">목록</a>
    <a href="{% url 'posts:edit' post.id %}">수정하기</a>
</body>
```

- 수정하기 버튼은 a element로 만들어주고 edit이라는 name의 url pattern은 post 객체의 id가 필요하니까 추가해준다.


- 이렇게 위의 코드를 입력하고 게시글 하나 상세 페이지에서 수정하기 버튼을 누르면, edit template이 출력이 되고 author와 body의 값이 채워져 있다. 그 다음 내용을 수정하고 수정하기 버튼을 누르면 데이터베이스의 데이터가 수정이 되면서, detail template으로 redirect 하게 된다.

* * *
## CRUD 중 Delete
- 먼저 삭제를 위한 url pattern를 추가해줘야 한다. 삭제 역시 하나의 게시글을 삭제하는 것이니까 post 객체의 id가 url에 필요하다.
- delete view 함수에서는 id로 해당 객체를 조회하고, 삭제를 해준다.

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
    # 게시글 수정 페이지
    path('<int:pk>/edit/', views.edit, name='edit'),
    # 게시글 수정 HTTP Request
    path('<int:pk>/update/', views.update, name='update'),
    # 게시글 삭제
    path('<int:pk>/delete/', views.delete, name='delete'),
]
```

- 위와 같이 delete url pattern를 만들어주고, 이제 views.py로 가서 delete 함수를 정의해주자.

```python
# 게시글 삭제 기능
def delete(request, pk):
    post = Post.objects.get(id=pk)
    post.delete()

    return redirect('posts:index')
```

- 전달받은 post 객체 id로부터 게시물 하나를 조회하고 post.delete() 함수를 사용해서 해당 데이터를 데이터베이스에서 삭제시켜준다.
- 그리고 게시글의 목록을 보여주는 index.html로 redirect 해준다.

```html
...

    <a href="{% url 'posts:index' %}">목록</a>
    <a href="{% url 'posts:edit' post.id %}">수정하기</a>
    <a href="{% url 'posts:delete' post.id %}">삭제하기</a>
...
```

- 마지막으로 detail.html에서 삭제할 수 있는 버튼을 만들어 놓는다.

- 위의 코드들을 전부 입력하고 설정하면, 이제 게시글 상세 페이지에서 삭제하기 버튼을 누르면, 해당 게시글이 삭제되고 목록 페이지로 돌아가게 된다.
