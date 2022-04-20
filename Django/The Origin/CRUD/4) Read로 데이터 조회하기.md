## Read로 데이터 조회하기
- 글 데이터를 list로 보여주기
- **views.py에 우리가 post_list_view라고 list를 만들기 위한 View를 설정했다. 여기서 코드를 입력하자.**

```python
...
def post_list_view(request):
    post_list = Post.objects.all()
    context = {
        'post_list': post_list,
    }

    return render(request, 'posts/post_list.html', context)
```

- 위의 코드처럼 Post 모델 데이터를 전부가져와서 변수를 넘겨주기.

- **그 다음은, post_list.html로 가서 보내준 변수를 활용한다.**

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}글 목록{% endblock %}

<!-- content block -->
{% block content %}
<h1>Post 목록 화면</h1>

{% for post in post_list %}
    <div>
        {{ post.id }}
        {{ post.image }}
        {{ post.content }}
        {{ post.created_at }}
        {{ post.writer }}
    </div>
{% endfor %}

{% endblock %}
```

- 이렇게 보내준 변수 post_list를 for문을 사용해서 하나씩 꺼내서 출력해볼 수 있다. post를 하나로 꺼내서 Post 모델의 필드를 출력할 수 있다.


### django ORM filter 사용해보기
- 다시 views.py의 post_list_view로 돌아가자. 

```python
def post_list_view(request):
    # post_list = Post.objects.all()   # Post 전체 데이터 조회
    post_list = Post.objects.filter(writer=request.user)    # Post.writer가 현재 로그인된 경우 조회
    context = {
        'post_list': post_list,
    }

    return render(request, 'posts/post_list.html', context)
```

- **이렇게 post_list = Post.objects.filter(writer=request.user) filter를 사용해서 --> Post 모델 데이터 중, 지금 내가 로그인한 유저와 작성자가 같은 경우를 조회해주는 것이다.**
  - **그래서 코드를 이렇게 설정하고 어드민 페이지에서 로그인한 뒤, 브라우저를 보면 지금 로그인된 유저가 작성한 글만 볼 수 있게 된다.**

- 이렇게 리스트를 출력해볼 수 있다.
