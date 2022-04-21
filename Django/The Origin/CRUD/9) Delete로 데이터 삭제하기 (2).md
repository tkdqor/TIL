## Delete로 데이터 삭제하기
- views.py에서 post_delete_view 설정하기

```html
def post_delete_view(request, id):
    post = get_object_or_404(Post, id=id)

    if request.method == 'GET':
        context = {
        'post': post,
        }
        return render(request, 'posts/post_confirm_delete.html', context)
    else:
        post.delete()
        return redirect('index')
```

- **이렇게 POST 방식일 때는 post.delete()로 글을 삭제해주면 된다. 그러면 삭제 버튼을 누르면 삭제가 된다.** 


- 그리고 우리가 글을 생성할 때만 @login_required 를 붙여줬는데, 수정이나 삭제할 때도 로그인이 된 유저만 가능해야 하니까 다 붙여주자.


### 로그인된 유저와 해당 글 작성자가 같을 때만 삭제 가능하게 하기
- post_delete_view 함수에서 if문으로 설정해주자.

```python
from django.http import Http404     # Http404를 import
...

@login_required                  # 로그인 되었을 경우에만 가능하게 하기
def post_delete_view(request, id):
    post = get_object_or_404(Post, id=id)

    if request.user != post.writer:
        raise Http404('잘못된 접근입니다.')

    if request.method == 'GET':
        context = {
        'post': post,
        }
        return render(request, 'posts/post_confirm_delete.html', context)
    else:
        post.delete()
        return redirect('index')
```

- **if request.user != post.writer: 이렇게 로그인된 유저와 글 작성자가 다를 때는, Http404 에러를 발생시켜주기.**

<img width="392" alt="image" src="https://user-images.githubusercontent.com/95380638/164421322-4716caae-2c38-4752-9ae7-1666789f0fbd.png">


* * *
- **이번에는 post_update_view에서 다르게 막아보자.** 브라우저에 그냥 http://localhost:8000/posts/5/edit/ 이렇게 입력하면 원래 들어가면 안되지만(로그인된 유저와 글 작성자가 다르기 때문에)
  지금은 들어가진다. 이걸 막기 위해서

```python
@login_required                  # 로그인 되었을 경우에만 가능하게 하기
def post_update_view(request, id):
    # post = Post.objects.get(id=id)
    post = get_object_or_404(Post, id=id, writer=request.user)
    ...
```

- **post = get_object_or_404(Post, id=id, writer=request.user) 이렇게 post 데이터를 가져올 때, writer를 무조건 request.user로 설정하면 들어갈 수 없게 된다.**





