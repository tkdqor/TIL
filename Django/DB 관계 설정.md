## DB 관계 설정
- RDB에서는 테이블과 테이블간의 관계를 설정해줄 수 있다.

## 1:N 관계(One to Many Relationship)

- ex) 모든 게시물을 반드시 로그인한 유저만 작성할 수 있다고 했을 때, 1명의 유저는 -> 여러 개의 게시물을 작성할 수 있다. 그러나, 하나의 게시물에 여러명의 유저가 작성할 수는 없다.
- 따라서, User 모델과 - 게시물 관련 Post 모델이 이러한 관계를 설정할 수 있다. (또한, 1개의 글에 여러개의 댓글이 달리는 case를 생각할 수 있다.)
- 그래서 각 테이블의 id인 Primary Key를 기준으로 두 테이블 간의 관계를 설정하면 되는데,   
  Post 테이블에 있는 user_id 필드(column)처럼 다른 테이블의 row를 식별할 수 있게끔 해주는 필드를 **"Foreign Key"** 라고 한다.

```python
class Post(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, null=True)
    body = models.TextField()
    created_at = models.DateTimeField()

    def __str__(self):
        return f'{self.user.get_username()}: {self.body}'
```

- 다음과 같이, Post 모델의 경우 ForeignKey라는 함수를 사용해서 User라는 모델과 관계를 설정하고(User 모델과 연동)   
  -> Post 테이블에 User 모델의 Primary Key인 user_id라는 새로운 column이 생성되는 것이다.
- 또한, 활용시에는 post.user 이렇게 사용하고 / 단순히 user_id만 담겨있는 것이 아니라 그 id에 해당하는 user 인스턴스 자체에 대한 정보가 담겨있다고 보면 된다.

- ForeignKey 함수의 두번째 인자인 on_delete=models.CASCADE는, 연동시킨 유저가 삭제되었을 때 해당 유저가 작성한 모든 게시글도 동시에 지워주는 설정이다.

* 만약, default값을 설정해야 된다고 하는 경우, null=True를 추가해서 -> nullable, 즉 user가 생성되지 않아도 모델 구성을 할 수 있게 해주면 된다.

```python
class Post(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, null=False)
    ...
```

- 다시 Post 모델에서 user_id가 없으면 글이 생성되지 않게 하려면, null=False 값을 주고 다시 makemigrations 및 migrate를 해주면 된다.

* * *
```html
{{ post.user.get_username }}
```

- html에서 Post 모델의 user 필드 데이터를 가져오려면 이러한 형식으로 불러오면 된다.

* * * 

- **1:N 관계가 맞는지 파악할 때는 -> FK를 설정한 테이블에서 1에 해당하는 테이블의 PK가 중복될 수 있는지 생각해보자.**   
  ex) Post 모델 테이블에서 User 모델의 PK는 중복될 수 있다. 1번 user가 여러 개의 글을 작성할 수 있기 때문이다.


* * * 
## M:N 관계(Many To Many Relationships)
- 인스타그램이나 페이스북의 좋아요 기능처럼, 1명의 유저는 여러개의 게시물에 좋아요를 누를 수 있고 / 1개의 게시물에는 여러명이 좋아요를 누를 수 있는 것과 같은 관계를 M:N 관계라고 한다.(또한, 유저 간 팔로우 기능을 생각해볼 수 있다.)
- 일반적으로, 이러한 M:N 관계를 구성하기 위해서 -> 관계가 있는 2개의 양 테이블 사이에 중간 테이블을 추가로 생성하게 된다.
  - User와 Post 모델이 있을 때, 종아요를 누르는 주체인 user_id와 / 해당 유저가 좋아요를 누른 게시물을 의미하는 post_id   
    -> 이렇게 2개의 column으로 구성된 새로운 중간 테이블을 생성하는 것이다.
- 이렇게 중간의 별도의 테이블을 만들고 모델 간 연동을 해줘야하는 복잡한 절차이지만, django에서는 코드 1줄이면 완성할 수 있다.

```python
class Post(models.Model):
  
  user = models.ForeignKey(User, on_delete=models.CASCADE, null=False)
  body = models.TextField()
  created_at = models.DateTimeField()
  liked_users = models.ManyToManyField(User, related_name='liked_posts')
```

- 위의 코드와 같이 서로 연결하고자 하는 2개의 모델 중 한 곳에서 ManyToManyField 함수를 사용하면 된다. 함수의 첫번째 인자로 연결하고자 하는 모델을 입력하면 된다.
- User 모델은 커스텀하기 어렵기에 Post 모델에 적용한 예시이다. 새롭게 liked_users라는 column를 생성했다.
- 반대로 User 모델에서는 related_name으로 liked_posts라는 column이 새롭게 생성된 것으로 생각하면 된다.
  - 각각 post.liked_users / user.liked_posts 이렇게 사용할 수 있다.

-> 이렇게 구성하게 되면, 실제 데이터베이스 상에서는 User 모델 - Post 모델 사이에 Like 모델(이름은 가상으로)이 만들어지고 user_id / post_id를 column으로 가지게 된다.
