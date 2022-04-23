## if문으로 로그인된 유저와 작성자 비교하기
- if문으로 로그인된 유저와 게시글 작성자가 같다는 조건을 주고 싶을 때가 있다.
- **ex) 예를 들어 User 모델과 Customer 모델이 1:1 관계이고 / Customer 모델과 Post 모델이 1:N 관계라고 가정해보자.** 
  - 이 상황에서 template에서 로그인된 유저와 게시글 작성자가 같은지 조건을 주고 싶을 때는,

```python
# 공지사항 게시판 글 조회하기
def board_detail(request, post_id):
    post = Post.objects.get(id=post_id)
    user = request.user.customer        # 로그인된 유저의 customer 정보 가져오기
    author = post.author                # 게시글 작성자 정보 가져오기
    print(user == author)               # True로 출력이 되므로 위의 2개의 변수가 서로 같다는 것을 확인

    context = {
        'post': post,
        'user': user,
        'author': author,
    }

    return render(request, 'posts/board_detail.html', context)
```

- **먼저 View에서 user = request.user.customer 이렇게 로그인된 유저의 customer 정보를 가져오고 / author = post.author 이렇게 게시글 작성자의 정보를 가져온다. 이 때, 게시글 작성자는
  Customer 모델과 관계가 되어있기 때문에 --> user와 author는 같은 유저라고 볼 수 있다.** 이 상태에서,
  
```html
<!-- 수정 및 삭제 버튼 -->
        <!-- if문으로 로그인된 유저와 해당 글 작성자가 같은 경우에만 수정 및 삭제 버튼이 보이게끔 하기 -->  
        {% if user == author %}
        <div class="edit_buttons">
            <div><button type="button" id="edit_button" class="btn" onclick=location.href="">수정</button></div>
            <div><button type="button" id="edit_button" class="btn" onclick=location.href="">삭제</button></div>
        </div>
        {% endif %}
```

- 이렇게 template에서 보내준 변수로 if문을 작성할 수 있다.
