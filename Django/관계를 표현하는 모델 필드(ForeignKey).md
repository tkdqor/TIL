## 알아야 할 사항
- ORM은 어디까지나 SQL 생성을 도와주는 라이브러리이다. ORM이 DB에 대한 모든 것을 알아서 처리해주지는 않는다.
- 보다 성능이 높은 애플리케이션을 만들고자 한다면, 사용하는 DB엔진과 SQL에 대한 높은 이해가 필요하다. 우리가 ORM을 통해서 직접 SQL를 작성하지는 않지만, ORM를 통해 생성된 SQL에 대해서 잘 이해하고 사용할 수 있어야 된다.


## RDBMS에서의 관계 예시(관계형 데이터베이스에만 있는 개념)
- **1:N 관계 : models.ForeignKey라고 표현** 
  - 1명의 유저(User)가 쓰는 다수의 포스팅(Post) / 그래서 User가 1, Post가 N이라고 표현. N의 모델에서 1에 해당하는 모델이 중복될 수 있다.
  - 1명의 유저(User)가 쓰는 다수의 댓글(Comment) 
  - 1개의 포스팅(Post)에 다수의 댓글(Comment)
  - ex) 1명의 User / 1개의 Post / 1개의 Comment가 있다고 가정했을 때, User와 Post간의 관계는 1:N 관계이고 / User와 Comment간의 관계도 1:N 관계이고 / Post와 Comment간의 관계도 1:N 관계이다.
    - 이러한 관계를 표현할 때는, 외래키(ForeingKey)라는 필드를 통해서 모델에서 표현할 수 있다. -> N측에다가 외래키를 심으면 된다.
    - User와 Post에서는 Post에다가 외래키를 정하고, Post와 Comment와의 관계는 Comment쪽에 정하고, User와 Comment관계에서는 Comment에다가 정하면 된다.


- **1:1 관계 : models.OneToOneField로 표현**
  - 1명의 유저(User)는 1개의 프로필(Profile)을 가지는 경우이다.
  - User와 Profile간의 관계는 1:1 관계이니까 이 관계를 User측에다가 정해도 되고 / Profile측에 이 관계를 명시해도 된다.
    - 그런데, django에서의 User 모델은 기본적으로 django.contrib.auth 앱에서 User 모델을 지원해준다. 그래서 우리가 정의하는 게 아니고, django가 정의한 User 모델이기 때문에 우리가 이 User 모델을 변경할 수 없다.
    - 물론 django에서는 커스텀 User 모델을 따로 만들어서 사용할 수 있는 설정이 있다. 하지만 그건 사용하는 User 모델의 클래스를 변경하는 것일 뿐, 해당 클래스 자체를 변경하는 건 아니다. 필드 자체를 바꾸는 건 아니다.
    - 그래서 User - Profile 처럼 1:1 관계는 둘 다 설정해도 되지만, Profile 측에다가 User에 대한 관계를 OneToOneField로서 정의한다.


- **M:N 관계 : models.ManyToManyField로 표현**
  - 1개의 포스팅(Post)에는 다수의 태그(Tag) / 1개의 태그(Tag)에는 다수의 포스팅(Post)이 존재하는 경우를 의미.
  - Post와 Tag간의 관계는 M:N 관계.


### ForeignKey
- 1:N 관계에서 N측에 명시 -> 관계는 명시하기 나름이다. Category와 Post는 1:N관계로 설정할 수도 있고, 혹은 ManyToMany 관계로 설정할 수도 있다.
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
- 여기서 실제로 생성되는 DB 필드는 -> post_id라는 필드가 생성된다.


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

- **또한, relation에서 post = models.ForeignKey(Post, on_delete=models.CASCADE) -> 여기서의 post라는 이름은 가상의 필드이다. 실제 데이터베이스 필드와는 다르다.**
- **그리고 post라는 필드에 저장되는 실제 필드값은 Post 모델의 pk값이 저장되기 때문에 1,2,3 이렇게 값이 올라갈 것이다.**
  - **Primary Key는 기본적으로 1부터 1씩 증가하는 숫자로 되어 있다. 그리고 ForiegnKey는 어떤 모델에서 다른 모델의 Primary Key를 사용할 때를 의미한다. 따라서 기본적으로는 기본키가 숫자이니까 다른 모델의 외래키도 숫자로 이루어짐을 알 수 있다.**
