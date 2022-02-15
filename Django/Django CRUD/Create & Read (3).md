## Form으로 전송한 데이터를 데이터베이스에 생성하기
- new.html의 form element에서 method를 GET이 아닌 POST 방식으로 바꿔주자.

```python
<form method="POST" action="/posts/create">
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

- 그리고나서 View 함수의 내용을 수정하자. 이제 POST 방식으로 Http Request가 전송되기 때문에 request.GET이 아닌 request.POST로 수정해야 한다. POST로 보낸 데이터를 별도의 변수에다가 저장해주자.

```python
from django.shortcuts import render, redirect

# 게시글 생성 기능
def create(request):
    author = request.POST.get('author')       # request.POST / 여기까지 딕셔너리의 형태이고 .get() 함수를 사용해서 안전하게 접근
    body = request.POST.get('body')

    post = Post(author=author, body=body)     # created_at 필드는 자동 설정 되어있음
    post.save()                               # Post(author=author, body=body, created_at=timezone.now()) timezone 모듈안에 now라는 함수   

    return redirect('posts:detail', pk=post.id)
```

- 그리고 author와 body라는 변수를 활용해서 -> 새로운 게시물을 생성하자. post = Post(author=author, body=body) 이런식으로 Post 테이블 모델에 새로운 데이터를 생성하기 위해 인스턴스를 만들어준다.
  추가로 post.save()까지 해주면 데이터베이스에 저장된다.

- 데이터가 생성된 이후, 별도의 안내 페이지가 아닌 우리가 만들어놓은 상세 페이지를 보여주자.
  - 이렇게 하기 위해서는 redirect 함수를 사용해서 다른 url로 보내주면 된다.
  - redirect 함수는 방향을 다시 지시하는 것으로 최종적으로 이동해야 할 장소를 우리가 다른 곳으로 변경시키는 것이다. 꼭 데이터를 보내야 하는 View 로직이 아니라서 render 함수를 사용할 필요가 없다.
  - 그래서 게시물을 생성한 다음에 return redirect('posts:detail', pk=post.id) -> redirect 함수로 posts앱의 detail url로 보내준다. 그리고 detail url은 post 객체의 id가 필요하기 때문에 
    url에 있는 변수에 새로 생성한 post의 id인 post.id를 저장해준다.



