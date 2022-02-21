## 1:N & M:N (1)
- django 모델 간 관계 설정하기
- 현재는 글을 새로 작성할 때 매번 작성자를 적어줘야 했는데, 이 부분을 User 모델을 이용해 개선해보자. 글을 쓰는 시점의 로그인한 유저의 username를 활용하는 것이다.


### 1:N 관계(One to Many Relationship)
- **RDB에서는, 테이블과 테이블간의 관계를 맺어줄 수 있는데, 모든 게시물은 반드시 로그인한 유저가 작성한다고 했을 때 -> 1명의 유저는 여러 개의 게시물을 작성할 수 있다. / 반면에 1개의 게시물에 여러명의 작성자는 불가능하다.**
- **이렇게 User 모델과 Post 모델이 1:N 관계를 맺게 되면, Post 모델의 author와 같은 필드를 User 모델 테이블에 저장해두고 Post에서는 해당 게시물을 작성한 유저가 몇 번 id를 가진 유저인지 저장하면 된다.**
  - **이런식으로 유저와 관련된 정보는 전부 User 테이블에 / 게시물과 관련된 정보는 전부 Post 테이블에 저장하고 각 테이블의 Primary Key를 기준으로 두 테이블 간의 관계를 설정하면 데이터가 중복 저장되는 것을 막을 수 있다.**

- Post 테이블에 있는 user_id 필드처럼 다른 테이블의 row를 식별할 수 있게끔 해주는 필드를 바로 **Foreign Key**라고 한다.



### 예시
- Post 모델에서 author 라는 필드를 제거하고 user_id와 같이 해당 게시물을 누가 작성했는지 나타내는 User 모델의 pk값만 들어 있게끔하는 필드로 변경해주면 된다.

```python
from django.contrib.auth.models import User

class Post(models.Model):
    
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    title = models.CharField(max_length=50, null=True)
    body = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f'{self.author}: {self.title}: {self.created_at}'
```

- 기존의 author라는 필드를 삭제하고 새롭게 user라는 필드를 설정
  - **ForeignKey()를 입력하고 첫번째 인자로 1:N관계를 맺을 User모델을 입력 / 그리고 두번째 인자로 on_delete=models.CASCADE를 주게 되면, 연동된 유저가 삭제되었을 때 해당 유저가 작성한 모든 게시물도 
    동시에 지워주게 된다.**
    
- 그리고 models.py에서 설정한 필드의 이름이 -> 우리가 실제로 python 코드로 django 모델을 활용할 때의 변수명이 된다. 
  - ex) post.user / post.title / post.body / post.created_at 처럼 사용가능하다.

- **또한, django는 모델 클래스의 모습을 해석해서 어떻게 데이터베이스에 저장되어야 하는지를 분석하는데, 이 과정에서 실제 DB내에 있는 테이블의 각각의 필드 이름을 자동으로 생성하게 된다.**
  - 그래서 우리가 models.py에 있는 Post 모델 클래스에서는 user, title, body, created_at이라고 작성했으나 -> 실제 데이터베이스에 저장될 때에는 동일한 이름을 가진 column이 생성될 수도 있고, 
    **user의 경우에는, 실제 데이터베이스에 user라는 이름을 가진 column이 생성되는 것이 아니라 user_id와 같은 이름이 바뀐 형태로 column이 생성될 수도 있다.**
  - 이러한 부분은 자동생성 되는 것이라 우리가 신경 쓸 필요는 없다.
    - django의 ForeignKey 함수를 통해서 User 모델과 Post 모델을 연동하게 되면 -> Post 테이블에 user_id라는 column이 생성되고 여기에는 연동된 유저의 PK값만 저장되지만, 실제로 우리가 python 코드로
      활용할 때에는 **post.user 이렇게 user_id가 아니라, 우리가 models.py에서 설정했던 user라는 변수 이름을 이용하게 되므로 DB에 자동생성 되는 것을 생각할 필요가 없다.**
    - **post.user -> 이렇게 사용할 경우에는 user_id만 담겨있는 것이 아니라, 그 user_id인 pk값에 해당하는 User 모델 user 인스턴스 자체에 대한 모든 정보가 담겨있는 것이라고 볼 수 있다.**
