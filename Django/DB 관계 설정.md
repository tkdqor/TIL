## DB 관계 설정
- RDB에서는 테이블과 테이블간의 관계를 설정해줄 수 있다.

## 1:N 관계(One to Many Relationship)

- ex) 모든 게시물을 반드시 로그인한 유저만 작성할 수 있다고 했을 때, 1명의 유저는 -> 여러 개의 게시물을 작성할 수 있다. 그러나, 하나의 게시물에 여러명의 유저가 작성할 수는 없다.
- 따라서, User 모델과 - 게시물 관련 Post 모델이 이러한 관계를 설정할 수 있다.
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
