## Delete로 데이터 삭제하기
- 먼저 urls.py로 가서 delet와 관련된 url에 name을 설정해주기.

```python
urlpatterns = [
    path('', post_list_view, name='post-list'),
    path('create/', post_create_view, name='post-create'),
    path('<int:id>/', post_detail_view, name='post-detail'),
    path('<int:id>/edit/', post_update_view, name='post-update'),
    path('<int:id>/delete/', post_delete_view, name='post-delete'),
]
```


### post_delete_view 수정하기
- 그리고 views.py에 있는 post_delete_view 함수를 수정해준다.

```python
def post_delete_view(request, id):
    post = get_object_or_404(Post, id=id)

    if request.method == 'GET':
        context = {
        'post': post,
        }
        return render(request, 'posts/post_confirm_delete.html', context)
    else:
        pass

```

- **전 강의처럼 Post 모델의 데이터 1개를 가져올 때, post = get_object_or_404(Post, id=id) 이렇게 404에러를 보내주도록 설정하기.**
  - 그리고 GET방식으로 요청이 올 때는 post 데이터를 보내주고 post_confirm_delete.html 페이지를 랜더링 해준다.

- **그 다음, post_card.html에서 삭제 버튼을 추가해준다.**

```html
...
{% if request.user == post.writer %}
            <a href="{% url 'posts:post-update' post.id %}">수정하기</a>
            <a href="{% url 'posts:post-delete' post.id %}">삭제하기</a>
{% endif %}
```

- 이렇게 추가해준다.


- **그리고나서, post_confirm_delete.html 파일을 수정해준다.**

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}글 삭제{% endblock %}

<!-- content block -->
{% block content %}
<h1>Post 삭제 화면</h1>
<form action="" method="POST">
    {% csrf_token %}
    <p>한번 삭제된 데이터는 복구가 불가능합니다.</p><br>
    <p>그래도 삭제하시겠습니까?</p><br>
    <a href="{% url 'index' %}">돌아가기</a>
    <input type="submit" value="삭제하기">
</form>
<hr>
{% include 'mixin/posts/post_card.html' with detail=True %}
{% endblock %}
```

- **위의 코드처럼 form element를 사용해서 POST로 보내주고 / include를 사용해서 우리가 만든 card template를 가져온다.**
  - 돌아가기 버튼을 만들어주기.

- 여기까지 삭제를 하는 화면에 관련해서만 코드를 입력해주었다.


