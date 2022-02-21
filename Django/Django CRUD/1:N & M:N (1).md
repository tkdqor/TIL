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
        return f'{self.user.get_username()}: {self.title}: {self.created_at}'
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

- 던더str메소드에서는, admin 페이지에서 username를 보여주기 위해 self.user.get_username() 이렇게 함수를 호출하면 된다.

* * *
- 위의 모델을 설정하고 python manage.py makemigrations posts를 하게 되면 -> 비어있으면 안되는 user 필드에 default를 설정하지 않았다고 나온다. 
```terminal
You are trying to add a non-nullable field 'user' to post without a default; we can't do that (the database needs something to populate existing rows).
Please select a fix:
 1) Provide a one-off default now (will be set on all existing rows with a null value for this column)
 2) Quit, and let me add a default in models.py
 ```

- **즉, 지금은 Post 모델 내부에 user라는 필드만 생성했지 그 안에 데이터를 채워주지 않은 상태이다. 그래서 Post 모델에 따르면 user_id 필드에 반드시 값을 채워 넣어야 한다. 그런데 우리가 default값을 주지 않아서 데이터를 어떻게 채울지 모르겠다는 의미이다.**
  - 1)번은 기본적으로 지정할 default 유저 1명을 지정해준다는 것이고 2)번은 잠시 끄고 default를 설정하겠다는 것이다. 
  - **그래서 우리는 일단, user 필드를 -> nullable로 바꿔서 migration를 진행한 다음 기존에 있었던 post 데이터 각각에다가 유저 정보를 적당히 채워주고 다시 user 필드를 non-nullable로 설정해보자.** 그러면 유저 정보가 있을 때에만 게시물을 작성할 수 있게 설정하는 것이다.

```python
class Post(models.Model):
    
    user = models.ForeignKey(User, on_delete=models.CASCADE, null=True)
    ...
    
    def __str__(self):
        if self.user:                     
            return f'{self.user.get_username()}: {self.title}'
        else:
            return f'{self.title}'
```

- 먼저, 이렇게 ForeignKey의 세번째 인자로 null=True를 설정하면 해당 게시물이 작성될 때 user 필드가 채워지지 않아도 게시물을 생성할 수 있게 된다.
- 그리고 던더str 메소드도 user데이터가 있을 때와 없을 때로 구분한다.

- 이러한 설정 이후에 migration를 하면 파일이 생성된다. 이어서 migrate까지 진행하자.
  - 그리고 난 다음에는 admin 페이지를 보면 각 게시글마다 user를 설정해줄 수 있다. 다 설정하게 되면 이제 유저가 연동되지 않은 게시글은 없기 때문에 다시 models.py로 가서 null옵션을 False로 수정하자.

- 그 다음 migration을 해주면, 
```terminal
You are trying to change the nullable field 'user' on post to non-nullable without a default; we can't do that (the database needs something to populate existing rows).
Please select a fix:
 1) Provide a one-off default now (will be set on all existing rows with a null value for this column)
 2) Ignore for now, and let me handle existing rows with NULL myself (e.g. because you added a RunPython or RunSQL operation to handle NULL values in a previous data migration)
 3) Quit, and let me add a default in models.py
 ```
 
 - 다음과 같이 또 출력이 되지만 2번 옵션이 추가되었다. 우리가 nullable로 user 필드를 변경한 다음, 직접 데이터 보정을 통해서 user가 null인 게시물이 없게끔 만들어주었기 때문에 문제가 없으니 2번을 선택해서 migration 파일을 만들자.
   - migrate까지 완료하면 이제 모든 Post 모델 데이터는 user 데이터가 있어야만 생성된다.

- 이제 template에서 post.author라고 되어있는 부분들을 -> post.user.get_username으로 수정해주자.

* * *
### 로그인을 한 상태에서만 글 작성
- posts 앱의 views.py에서 글쓰기 생성과 관련된 new, create View 함수 내부에 로그인 여부를 검증하는 코드를 입력하자.

```python
# 게시글 생성 페이지
def new(request):
    if not request.user.is_authenticated:     # 로그인이 되지 않은 경우, login url로 redirect
        return redirect('accounts:login')

    return render(request, 'posts/new.html')      


# 게시글 생성 기능
def create(request):
    if not request.user.is_authenticated:    # 로그인이 되지 않은 경우, login url로 redirect
        return redirect('accounts:login')

    user = request.user                      # 로그인된 유저 정보를 user라는 변수에다가 저장하기      
    body = request.POST.get('body')          # request.POST / 여기까지 딕셔너리의 형태이고 .get() 함수를 사용해서 안전하게 접근
    title = request.POST.get('title')

    post = Post(user=user, title=title, body=body)     # created_at 필드는 자동 설정 되어있음
    post.save()                               # Post(author=author, body=body, created_at=timezone.now()) timezone 모듈안에 now라는 함수   

    return redirect('posts:detail', pk=post.id)
```

- create함수에서는, user라는 변수에다가 request.user라는 로그인된 유저 정보를 저장해주고 실제로 데이터베이스에 데이터를 생성할 때, post = Post(user=user, title=title, body=body) 이렇게 user필드에 해당 변수를 넣어주자.

- new template에서도 author를 입력받는 input 코드를 삭제하기.

- 그러면, 



