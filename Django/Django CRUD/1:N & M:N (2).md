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

- 그리고 

