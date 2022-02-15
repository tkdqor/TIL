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


- 이제 이렇게 View 함수를 설정한 다음에, 다시 글쓰기 페이지에 가서 데이터를 입력하고 글쓰기 버튼을 누르면 -> 에러가 뜨게 된다.
  - 그 이유는, django에서는 POST 방식으로 데이터를 전송할 때 맨 마지막에 /를 붙이지 않으면 안되기 때문이다. 우리가 urls.py에도 마지막에 /를 붙여놓았다.
```html
<body>
    <h1>New Post</h1>

    <!-- <form method="POST" action="/posts/create/"> -->
    <form method="POST" action="{% url 'posts:create' %}">        
     ...
```

- 주석처리된 코드 처럼 직접 하드코딩 해도 상관없지만, url pattern naming을 통해 간단하게 작성해보자.

- 그리고 추가로 생각해야 되는 점은, 바로 CSRF 검증이다.
  - 이 문제를 해결하기 위해서는 form element 내부에다가 

```html
<form method="POST" action="{% url 'posts:create' %}">
    {% csrf_token %}
     ...
```

- {% csrf_token %} 이렇게 작성해줘야 한다. 이제 다시 new.html에서 데이터를 넣고 버튼을 누르면 -> 데이터베이스에 데이터가 추가되면서 상세 페이지로 이동되는 것을 확인할 수 있다.
- 우리가 입력한 데이터로 새로운 게시물이 작성된다.

- 여기까지 CRUD 중에서 Create와 Read에 대해서 구체적으로 알아봤다.



* * *

## CSRF Token
- **CSRF란, Cross Site Request Forgery의 약자로 사이트 간 위조 공격을 의미한다.**
- 해커가 HTTP Request를 마치 우리가 전송하는 것처럼 흉내 내서 우리가 의도하지 않은 액션을 취하게 만드는 공격 기법이다. 
  - **이를 막기 위해서는 security 토큰이라고 불리는 숨겨진 암호화 토큰을 form element 내부에다가 삽입한다. 그렇게하면, 우리가 정상적으로 form element를 사용할 때에는 이 숨겨진 토큰 데이터가 같이 server로 넘어오기 때문에, server에서는 이 토큰 유무를 확인해서 정상적인 요청인지 해커의 공격인지 구분할 수 있게 된다.**
  - 만약, 공격자가 외부에서 HTTP Request를 흉내만 냈을 경우에는 해당 Request에 우리가 심어뒀던 security 토큰이 포함되어 있지 않을 것이다. 그래서 유효하지 않은 Request가 되서 server에서는 해당 Request를 거부하면 된다.
  - 그래서 django에서는 공격자의 request와 정상적인 request를 구분하기 위해서 form element를 사용할 때, 이 CSRF 토큰을 강제한다.
  - 공식문서 참고 https://docs.djangoproject.com/en/3.2/ref/csrf/



