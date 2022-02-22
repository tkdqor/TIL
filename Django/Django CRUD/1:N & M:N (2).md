## 1:N & M:N (2)

### M:N 관계 (Many to Many relationship)
- **1명의 유저는 여러 개의 게시물에 좋아요를 누를 수 있고, 하나의 게시물에는 여러 명의 유저가 좋아요를 누를 수 있다.**
- User 모델이나 Post 모델에 관련 정보를 추가하기에는 애매하다. 저장해야하는 user_id나 post_id가 한 두개가 아니기 때문이다.
- **그래서 일반적으로 M:N 관계는 -> 관계에 있는 테이블 사이에 중간 테이블을 새롭게 생성한다.**
  - 좋아요를 누르는 주체인 user_id와 해당 유저가 좋아요를 누른 게시물을 의미하는 post_id 정보만 뽑아서 기록해놓는 테이블이다. (해당 Like라는 테이블의 column이 pk / user_id / post_id)
  - 따라서 어떤 유저가 어떤 게시물에 좋아요를 눌렀는지 알 수 있다.


### M:N 관계 설정 방법
- **models.py에서 M:N 관계로 설정하고자 하는 모델 중 한곳에다가 ManyToManyField를 설정하면 된다.**
  - 즉, User 모델과 Post 모델은 -> 1:N 관계 + M:N 관계 모두 설정이 된다.

```python
class Post(models.Model):
    
    user = models.ForeignKey(User, on_delete=models.CASCADE, null=False)   # User모델과 1:N관계 설정 / user 데이터가 삭제되면 게시글도 삭제
    title = models.CharField(max_length=50, null=True)
    body = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    liked_users = models.ManyToManyField(User, related_name='liked_posts')
```

- **ManyToManyField 함수에는 첫번째 인자로 M:N 관계로 연결하고자 하는 모델 클래스를 입력해준다. 그리고 연결의 대상이 되는 User 모델에서는, 이 Post 모델과 어떠한 이름을 가진 어트리뷰트(필드)로 연결해 사용할지를 
  related_name이라는 옵션에 지정해주면 된다.** 
  - **실제 python 코드로 post 데이터와 user 데이터를 활용할 때는, post.liked_users / user.liked_posts -> 이러한 형태로 python 코드를 사용할 수 있다.**
  - post.liked_users라고 하면, 해당 게시물에 좋아요를 누른 사용자들과 관련된 작업을 수행할 있고 / user.liked_posts라고 하면, 해당 유저가 좋아요를 누른 모든 게시물과 관련된 작업을 수행할 수 있다.

- 이제, migration과 migrate을 실행해주자.
  - **그러면 이제 실제로 User 모델과 Post 모델을 M:N으로 연동하기 위해서 데이터베이스 내에 post_liked_users라는 이름의 테이블이 생성된다.**


### 좋아요 기능 구현
- posts 앱 내부 urls.py로 가서 다음과 같이 설정.
```python
app_name = 'posts'
urlpatterns = [
    # 좋아요 버튼
    path('<int:pk>/like/', views.like, name='like'),
]
```

- 그리고 posts 앱의 views.py로 가서 like View 함수 설정.
```python
# 좋아요 기능
@login_required                                         # 로그인을 했을 경우에만 좋아요 가능
def like(request, pk):
    if request.method == 'POST':                        # HTTP Request method가 POST일 경우에만,
        try:
            post = Post.obejcts.get(id=pk)              # 좋아요를 누를 게시글의 pk로 조회한 다음 인스턴스를 post에 저장

            if request.user in post.liked_users.all():  # 만약 로그인한 유저의 pk가 해당 글의 좋아요 유저 목록에 있다면
                post.liked_users.remove(request.user)   # 그 목록에서 삭제
            else:
                post.liked_users.add(request.user)      # 목록에 없다면 새롭게 추가

            return redirect('posts:detail', pk=post.id)

        except Post.DoesNotExist:
            pass

    return redirect('posts:index')              
```

- HTTP Request가 POST방식으로 전달될 경우에만, 좋아요를 처리. POST방식이 아니면 목록으로 redirect.
- 좋아요를 누를 대상의 게시글을 찾을 때, try - except로 예외 처리 진행.
- **:으로 끝나는 코드는 -> 그 다음줄에서 들여쓰기를 하고 실제로 수행할 코드를 반드시 작성해줘야 한다.**
  - 따라서 pass라는 코드를 들여쓰기해서 입력하면 별도로 코드가 실행되는 것 없이 지나가게 된다.

- if request.user in post.liked_users.all():  -> 만약 현재 로그인된 사용자가 해당 게시물에 좋아요를 누른 모든 유저의 목록에 있다면, 
  - **post.liked_users.remove(request.user)  -> 해당 게시물의 좋아요를 누른 유저 목록에서 삭제하라는 의미.**
  - **else: post.liked_users.add(request.user)  -> 만약 그렇지 않다면, 포함되어 있지 않다면 현재 로그인 되어있는 사용자를 좋아요를 누른 유저 목록에 추가하라는 것.**
  - **remove 함수와 add 함수는 내부적으로 save가 다 되는 함수이다. 즉, M:N 관계에서 중간 테이블의 값을 추가하거나 제거하기 위해서는 remove와 add 함수를 사용하면 된다.**


- 이제 좋아요 버튼을 만들기 위해 detail template에서 추가.
```html
    <!-- 해당 게시글의 좋아요 수 표시하기 -->
    <div class="mb-3 col-md-6">
        좋아요 수 : {{ post.liked_users.count }}
    </div>

    <!-- 좋아요 버튼 -->
    {% if user.is_authenticated %}
        <form class="mb-3" method="POST" action="{% url 'posts:like' post.id %}">
           {% csrf_token %}
           
           {% if user in post.liked_users.all %}
                <button type="submit" class="btn btn-primary">LIKE</button>
           {% else %}
                <button type="submit" class="btn btn-secondary">LIKE</button>
           {% endif %}     
        </form>
    {% endif %}    
```

- 좋아요 수는 로그인 여부와는 상관없이, 무조건 볼 수 있게끔 작성했다. 
  - **post.liked_users.count -> 이렇게 count 기능을 사용하면, 해당 게시물의 좋아요를 누른 유저들의 수를 알 수 있다.**

- 그리고 로그인이 되어있는 경우에만, 좋아요 버튼을 포함하고 있는 form element를 추가해준다.
  - {% if user in post.liked_users.all %} -> 원래는 all()이지만 django template language이기에 괄호를 생략. 현재 로그인되어있는 유저가 해당 게시물에 좋아요를 누른 유저 목록에 포함되어 있다면, 푸른색 버튼을 생성. / 좋아요를 누르지 않은 경우에는 회색 버튼 생성.
