## 알아야 할 사항
- ORM은 어디까지나 SQL 생성을 도와주는 라이브러리이다. ORM이 DB에 대한 모든 것을 알아서 처리해주지는 않는다.
- 보다 성능이 높은 애플리케이션을 만들고자 한다면, 사용하는 DB엔진과 SQL에 대한 높은 이해가 필요하다. 우리가 ORM을 통해서 직접 SQL를 작성하지는 않지만, ORM를 통해 생성된 SQL에 대해서 잘 이해하고 사용할 수 있어야 된다.


## RDBMS에서의 관계 예시(관계형 데이터베이스에만 있는 개념)
- **1:N 관계 : models.ForeignKey라고 표현** 
  - 1명의 유저(User)가 쓰는 다수의 포스팅(Post) / 그래서 User가 1, Post가 N이라고 표현. N의 모델에서 1에 해당하는 모델이 중복될 수 있다.
  - 1명의 유저(User)가 쓰는 다수의 댓글(Comment) 
  - 1개의 포스팅(Post)에 다수의 댓글(Comment)
  - ex) 1명의 User / 1개의 Post / 1개의 Comment가 있다고 가정했을 때, User와 Post간의 관계는 1:N 관계이고 / User와 Comment간의 관계도 1:N 관계이고 / Post와 Comment간의 관계도 1:N 관계이다.
    - **이러한 관계를 표현할 때는, 외래키(ForeingKey)라는 필드를 통해서 모델에서 표현할 수 있다. -> N측에다가 외래키를 심으면 된다.**
    - User와 Post에서는 Post에다가 외래키를 정하고, Post와 Comment와의 관계는 Comment쪽에 정하고, User와 Comment관계에서는 Comment에다가 정하면 된다.


- **1:1 관계 : models.OneToOneField로 표현**
  - 1명의 유저(User)는 1개의 프로필(Profile)을 가지는 경우이다.
  - User와 Profile간의 관계는 1:1 관계이니까 이 관계를 User측에다가 정해도 되고 / Profile측에 이 관계를 명시해도 된다.
    - 그런데, **django에서의 User 모델은 기본적으로 django.contrib.auth 앱에서 User 모델을 지원해준다.** 그래서 우리가 정의하는 게 아니고, django가 정의한 User 모델이기 때문에 우리가 이 User 모델을 변경할 수 없다.
    - 물론 django에서는 커스텀 User 모델을 따로 만들어서 사용할 수 있는 설정이 있다. 하지만 그건 사용하는 User 모델의 클래스를 변경하는 것일 뿐, 해당 클래스 자체를 변경하는 건 아니다. 필드 자체를 바꾸는 건 아니다.
    - **그래서 User - Profile 처럼 1:1 관계는 둘 다 설정해도 되지만, Profile 측에다가 User에 대한 관계를 OneToOneField로서 정의한다.**


- **M:N 관계 : models.ManyToManyField로 표현**
  - 1개의 포스팅(Post)에는 다수의 태그(Tag) / 1개의 태그(Tag)에는 다수의 포스팅(Post)이 존재하는 경우를 의미.
  - Post와 Tag간의 관계는 M:N 관계.

* * *

### ForeignKey
- **1:N 관계에서 N측에 명시** -> 관계는 명시하기 나름이다. Category와 Post는 1:N관계로 설정할 수도 있고, 혹은 ManyToMany 관계로 설정할 수도 있다.
- https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.ForeignKey 공식문서 참고

- **ForeingKey(to, on_delete)**
  - 외래키를 정할 때 다양한 옵션들이 있지만, 가장 대표적으로 to라는 인자와 on_delete라는 인자는 필수적으로 지정을 해야한다. 지정하지 않으면 migrations가 되지 않는다.
  - **to이라는 인자는 -> 1:N관계에서 1에 해당하는 모델의 클래스이름을 입력하면 된다.** 클래스명을 직접 지정하거나, 클래스명을 문자열로 지정할 수 있다. 또는 자기자신의 모델 테이블을 직접 참조해야 할 때는 self라는 이름으로 지정해주면 된다.


- Post와 Comment의 1:N 관계를 표현해보면,
```python 
class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE)     # post = models.ForeignKey('Post', on_delete=models.CASCADE) 이렇게도 가능하다.
    message = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

- 이렇게 Comment 모델에서 외래키가 Post 모델과 연관이 됨을 설정하고 on_delete=models.CASCADE라고 입력하고 post라는 필드를 설정해보자. 

- **post라는 필드는 -> 실제 DB에는 post가 아니라, post_id라는 이름의 필드로 반영된다. 그리고 1:N 관계에서 N에 해당하는 모델에만 새로운 필드가 생성되는 것이다.**
  - .ForeignKey(Post, on_delete=models.CASCADE) 이렇게 Post라고 입력하면 -> 현재 위치한 instagram 앱 안에서 Post라는 모델을 찾아서 참조를 하게된다. 아니면 .ForeignKey('instagram.Post', on_delete=models.CASCADE) 이렇게 앞에다가 앱 이름을 입력해도 된다. 이건 다른 앱의 모델을 가져오는 경우가 많다.


### on_delete 속성
- **on_delete는 Record 삭제 시 Rule를 의미한다. 즉, 1:N 관계에서 1쪽에 있는 record가 삭제가 될 때 -> N측에 있는 1에 속한 해당 record들을 어떻게 처리할지에 대한 rule을 지정하는 것이다.**
  - CASCADE = N측에 있는 ForeignKey로 참조된 다른 모델의 record도 삭제  (가장 많이 사용된다.)
    - 어떤 Post에 속한 Comment에서 Post가 하나 삭제되면, Comment들도 전부 다 삭제가 되도록 하는 옵션이라고 볼 수 있다.
  - PROTECT = ProtectedError(IntegrityError 상속)를 발생시키며, 삭제를 방지한다.
  - SET_NULL = null로 대체. 필드에 null = True 옵션이 필수이다. 
    - 1:N 관계에서 1이 삭제 되어도 N은 삭제되지 않고 그 관계를 null로 자동지정 하겠다는 의미이다.
  - SET_DEFAULT = 디폴트 값으로 대체. 필드에 디폴트값 지정을 필수로 해야 한다. (별로 사용되지는 않는다..)
  - SET = 대체할 값이나 함수 지정. 함수의 경우 호출하여 리턴값을 사용.
  - DO_NOTHING = 어떠한 액션 X. DB에 따라 오류가 발생할 수도 있다.


### 1:N 예시
- Post와 Comment의 1:N 관계에서 N측에는 실제 어떤 값이 저장이 되냐면, **관계를 정의할 때에는 실제 각각의 모델마다 pk라고 해서 Primary Key가 있다. 디폴트로 id라는 필드이다.** 
  - Post에도 각각의 레코드마다 pk가 있다. **1:N의 관계에서는 N쪽의 외래키에 1에 있는 Post의 pk를 저장해야 된다.** N쪽에서 저장할 필드명은 임의로 정할 수 있고 post_id 또는 post 이렇게 해줄 수 있다.
```python
class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    message = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

- 위의 코드와 똑같지만 다시 한 번 확인해보자.
- **여기서 실제로 생성되는 DB 필드는 -> post_id라는 필드가 생성된다.**


- 이제 실제로 migrations를 해보면,
```terminal
python manage.py makemigrations instagram
python manage.py sqlmigrate instagram 0004

CREATE TABLE "instagram_comment" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "message" text NOT NULL, "created_at" datetime NOT NULL, "updated_at" datetime NOT NULL, "post_id" bigint NOT NULL REFERENCES "instagram_post" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE INDEX "instagram_comment_post_id_41735a7d" ON "instagram_comment" ("post_id");
COMMIT;
```

- 위와 같이 쿼리문을 보면 -> comment라는 모델 테이블이 생기면서 id라는 필드가 Primary Key로 자동으로 생긴다. 
  - 그리고 뒤에는 post_id라는 필드가 생기게 된다.

- **또한, relation에서 post = models.ForeignKey(Post, on_delete=models.CASCADE) -> 여기서의 post라는 이름은 가상의 필드이다. 실제 데이터베이스 필드에는 post_id라는 필드로 생성이 된다.**
- **그리고 post라는 필드에 저장되는 실제 필드값은 Post 모델의 pk값이 저장되기 때문에 1,2,3 이러한 값이 저장될 것이다.**
  - **Primary Key는 기본적으로 1부터 1씩 증가하는 숫자로 되어 있다. 그리고 ForiegnKey는 어떤 모델에서 다른 모델의 Primary Key를 사용할 때를 의미한다. 따라서 기본적으로는 기본키가 숫자이니까 다른 모델의 외래키도 숫자로 이루어짐을 알 수 있다.**

* * *

### 올바른 User 모델 지정
- 만약, 우리가 Post 모델에서 작성자를 지정하려고 한다면
```python
# from django.contrib.auth.models import User

class Post(models.Model):      
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    message = models.TextField()    
    photo = models.ImageField(blank=True, upload_to='instagram/post/%Y/%m/%d')                                    
    is_public = models.BooleanField(default=False, verbose_name='공개여부')    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
```    

- 이렇게 author라는 필드를 만들고 외래키로 User 모델을 지정할 수 있다.
  - User 모델을 사용하기 위해서 from django.contrib.auth.models import User 대부분 이렇게 import를 하는데 적용은 되지만, 권장되는 방법은 아니다. 왜냐면 django는 User 모델이 변경될 수도 있기 때문에 확실한 방법이 아니다. 이 User 모델말고도 딴 User 모델이 현재 django 프로젝트에 활성화되어 있을 수 있다.

- 그래서 만약 instagram 앱 내부에 User 모델을 변경해서 새롭게 만들었다면, instagram app 안에 있는 User 모델이 되니까 settings.py에다가 
```python
AUTH_USER_MODEL = 'instagram.User'
```

- AUTH_USER_MODEL이라는 이름의 변수를 만들고 해당 앱 User 모델이 있는 **'App이름.해당모델명'** 이렇게 써주어야 한다. 이 값은 항상 현재 활성화되어 있는 User 모델을 가리킨다.
  - 그리고 이 값의 dafault 값이 바로 **'auth.User'** 이다.
  - django 디렉터리 밑에 conf 디렉터리 밑에 globalsettings.py에 저장되어 있다. 그래서 다시 models.py를 수정해보면,

```python
from django.conf import settings

class Post(models.Model):      
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    message = models.TextField()    
    photo = models.ImageField(blank=True, upload_to='instagram/post/%Y/%m/%d') 
    is_public = models.BooleanField(default=False, verbose_name='공개여부')      
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
```    

- settings.AUTH_USER_MODEL은 -> 문자열로 'auth.User'와 같다. 
  - 실제로 settings.py에 따로 추가하지 않았지만, default값이 'auth.User'이기 때문에 상관없다.
- 그리고 import는 from django.conf import settings 이렇게 해주는 것이다. 
- 이렇게 사용하는 것이 제일 안전하고 django에서 User 모델을 지정하는 확실한 방법이다.

* * *

### FK에서의 reverse_name
- Post와 Comment의 1:N 관계에서 Comment는 현재 post_id라는 필드가 있다. 이 필드에 접근을 해보자.
```terminal
python manage.py shell 

In [1]: from instagram.models import Post, Comment

In [2]: Comment.objects.all()
Out[2]: <QuerySet []>
```

- 일단 이렇게 django 연동 shell로 Comment 모델에 빈 데이터를 확인해봤다. 

- 그리고 이제 admin.py에 Comment 모델을 등록시키자.
```python
from .models import Post, Comment   

@admin.register(Comment)
class CommentAdmin(admin.ModelAdmin):
    pass
```

- 이렇게해서 admin 페이지에도 확인할 수 있다.

- 그래서, admin 페이지에 Comment 모델을 선택하고 -> Add 버튼을 눌러서 새로운 댓글을 생성해보자.
  - 그러면 post라는 필드에서 어떤 post의 댓글을 달지 선택할 수 있다. 예시로 네번째 포스팅에 첫번째, 두번째 댓글을 생성하자.

```terminal
python manage.py shell

In [2]: from instagram.models import Post, Comment

In [3]: Comment.objects.all()                                                       # 이렇게 입력하면 Comment 모델의 모든 row 데이터를 가져와준다.(1줄씩 여러줄..)
Out[3]: <QuerySet [<Comment: Comment object (1)>, <Comment: Comment object (2)>]>    

In [4]: Comment.objects.first()       # 이렇게 입력하면 Comment 모델의 첫번째 row 데이터를 가져와준다.
Out[4]: <Comment: Comment object (1)>

In [5]: comment = Comment.objects.first()

In [6]: comment.post
Out[6]: <Post: 네번째 포스팅>
```

- 이제 다시 queryset으로 조회해보면, 이렇게 2개의 댓글 데이터가 확인된다. 
  - 그리고 **Comment.objects.first() 이렇게 작성하면 첫번째 Comment 객체를 가져오게 된다. 즉, Comment 모델 테이블의 1개의 행을(1줄을) 조회한 것이라고 보면 된다.**
  - 다시 comment라는 변수에 첫번째 Comment 객체를 저장하고, Comment 모델 안에 있는 post라는 필드가 있기 때문에 **comment.post 이렇게 하면 post의 객체를 가져오게 된다.**
    - **comment.post -> 이렇게 입력하는 것은, Comment 모델의 1줄의 row 데이터를 comment에 저장하고, 그 1줄에서 post라는 필드에 있는 pk값, 숫자를 가져오는 것이 아니라 -> Post 모델에서 그 pk값에 해당하는 row, 행 데이터 1줄을 가져와주는 것이다.**

* * *

- **comment.post가 어떤 원리로 동작이 되는것일까.**
  - 해당 comment 모델 객체가 아직 post라는 객체를 만들지 않았다라면, post_id라는 값을 가지고(comment.post_id) -> Post.objects.get(pk=comment.post_id) 이렇게 되서 <Post: 네번째 포스팅> 을 가져오게 된다. 이 동작을 내부적으로 수행해서 post에다가 자동으로 할당을 해준다. 
```
In [6]: comment.post
# Post.objects.get(pk=comment.post_id)
Out[6]: <Post: 네번째 포스팅>
```

- **그래서 우리가 매번 위와 같은 과정을 해야되는데, 이런 걸 하지 않아도 그냥 comment.post를 했을 때 쉽게 post 객체를 가져올 수 있는 것이다.**
  - 여기서 Comment 모델의 post는 가상의 필드이다.

* * *

- **문제는 거꾸로이다.** 만약에
```terminal
In [7]: post = Post.objects.first()

In [8]: post
Out[8]: <Post: 네번째 포스팅>
```

- 이렇게 Post 모델의 첫번째 객체를 조회해서(default로 내림차순 되어있음) 네번째 포스팅을 조회했을 때, 우리가 이 네번째 포스팅의 속한 댓글을 어떻게 얻어올 것이냐에 대해서 고민하게 된다. 먼저,

```terminal
In [10]: Comment.objects.filter(post_id=4)
Out[10]: <QuerySet [<Comment: Comment object (1)>, <Comment: Comment object (2)>]>
```

- **이런식으로 filter를 post_id라는 필드로 접근해서 네번째 포스팅에 해당하는 댓글 row 데이터 2줄을 조회할 수 있다.** 혹은,

```terminal
In [11]: Comment.objects.filter(post__id=4)
Out[11]: <QuerySet [<Comment: Comment object (1)>, <Comment: Comment object (2)>]>
```

- **위와 같은 queryset은 post가 외래키이니까 post 모델에 속한 id / 즉, 실제 post 모델에 있는 pk값 id를 의미한다.**

- 그렇지만, 추천되는 방법은
```terminal 
# post = Post.objects.first()

In [12]: Comment.objects.filter(post=post)
Out[12]: <QuerySet [<Comment: Comment object (1)>, <Comment: Comment object (2)>]>
```

- 이렇게 가져올 수 있다. 

- 또한, 위의 내용들과 동일하게 결과값을 출력할 수 있는 방법은
```terminal
# post = Post.objects.first()

In [13]: post.comment_set
Out[13]: <django.db.models.fields.related_descriptors.create_reverse_many_to_one_manager.<locals>.RelatedManager at 0x10827c910>

In [14]: post.comment_set.all()
Out[14]: <QuerySet [<Comment: Comment object (1)>, <Comment: Comment object (2)>]>
```

- 이렇게 총 4가지 queryset은 같은 결과를 낸다. 마지막이 제일 편리하게 사용이 된다.

- **post.comment_set.all() -> Post 모델에서 첫번째 row 데이터를 post라는 변수에 담고, 1:N 관계에서 1에 해당하는 모델이 N에 해당하는 모델에 접근하기 위해서는 _set 함수를 사용하면 된다.**
  - **따라서, post에서 .comment_set으로 Comment 모델에 접근하고 -> Post 모델의 첫번째 row 데이터에 해당하는 pk값을 기준으로 Comment 모델에서 그 pk값에 해당하는 모든 Comment row 데이터를 가져오라는 것이 바로 post.comment_set.all()이다.**

### FK에서의 reverse_name이란
- reverse 접근 시의 속성명은 : default로는 **"N측 모델명소문자_set"** 이라는 것이 default로 생긴다.
  - Post와 Comment가 1:N일 때 -> comment_set 이라는 것이 생기게 된다.  
- 1:N의 관계에서 1쪽에서 사용하는 것이다. 1측에서는 참조할 모델명이 없다. ForeignKey 설정이 N쪽에 되어있기 때문이다.
- post.comment_set.all() <=> Comment.objects.filter(post=post)


### reverse_name의 이름 충돌이 발생한다면?
- reverse name의 디폴트 명은 앱이름은 고려되지 않고, 모델명만 고려된다.
  - ex) blog앱의 Post모델의 author필드  = FK(User) / shop앱의 Post모델의 author필드  = FK(User) -> 이런 상황일 때, user.post_set 이름에 대한 충돌이 발생한다.
  - 이름 충돌이 발생하게 되면, makemigrations가 실패된다.

- 그래서, 하나의 모델에 대해 다른 모델에서 외래키 참조를 한다면 -> 외래키를 지정할 때 어느 한쪽의 (혹은 모두) FK의 reverse_name을 변경해야 한다.
  - ex) FK(User, related_name="blog_post.set")
  - ex) FK(User, related_name="shop_post.set")
  - 이렇게 reverse_name이 충돌되지 않도록 하자.

- 또는, 어느 한쪽의 FK에 대해 reverse_name을 포기할 수 있다. -> related_name = '+' 이렇게 하면 reverse_name을 사용하지 않겠다는 의미이다.

* * *

### ForeignKey.limit_choices_to 옵션
- 외래키에서는 limit_choices_to라는 옵션이 있다. admin페이지에서 보면, Comment 모델에서 댓글 1개를 생성할 때 -> Post 필드에서 글을 choice 할 수 있게 된다. 지금은 전체가 조회되는데, 여기에 어떤 조건을 걸 수 있다는 것이다.
- Form을 통한 Choice 위젯에서 선택항목 제한 가능.
  - dict/Q 객체를 통한 지정 : 일괄 지정
  - dict/Q 객체를 리턴하는 함수 지정 : 매번 다른 조건 지정 가능

- ManyToManyField에서도 지원

```python
staff_member = models.ForeignKey(User, on_delete=models.CASCADE, limit_choices_to={'is_staff': True})
```

- 이렇게 사전을 지정할 수 있고, q객체를 지정할 수도 있다. 또는 사전이나 q 객체를 리턴하는 함수를 지정할 수도 있다.

```python
class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, limit_choices_to={'is_public': True})
    ...
```

- 이런식으로 설정하면 Comment add 버튼 누르고 난 다음, Post에서 is_public이 True인 포스팅만 선택할 수 있게된다.
