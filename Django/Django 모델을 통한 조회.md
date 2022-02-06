## django 모델을 통한 조회(데이터베이스 쿼리)
- 장고 모델은 Manager/QuerySet을 통해 다양한 SQL 쿼리를 수행할 수 있다.

## Model Manager
- 모델 매니저란, 데이터베이스 질의 인터페이스를 제공한다. 디폴트 Manager로서 **모델클래스.objects** 라는 attribute가 제공된다.
- ex) 
  - **모델클래스.objects.all()** -> SELECT * FROM app_model;  이라는 sql이 생성
  - **모델클래스.objects.all().order_by('id')[:10] (또는 .filter / .exclude)** -> SELECT * FROM app_model ORDER BY id DESC LIMIT 10;  이라는 sql이 생성
  - **모델클래스.objects.create(title="New Title")** -> INSERT INTO app_model (title) VALUES ("New Title");  이라는 sql이 생성

- 이러한 쿼리를 수행하는 데 있어 실행과 패치, 데이터베이스로부터 가져오고 하는 작업들은 전부 **모델클래스.objects.all()와 같은 queryset**이 다 수행을 해준다.

```terminal
python manage.py shell

>>> from instagram.models import Post
>>> Post.objects.all()
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>
```

- Post.objects.all() 등 이러한 queryset의 실제 쿼리를 보고 싶다면, 

```terminal
>>> qs = Post.objects.all()

>>> print(type(qs))
<class 'django.db.models.query.QuerySet'>

>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", 
"instagram_post"."updated_at" FROM "instagram_post"

>>> print(qs)
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>
```

- 먼저, Post.objects.all()와 같은 코드의 타입은 QuerySet이라고 확인되고, 해당 queryset에 대한 실제 sql문, 쿼리를 출력해볼 수 있다. -> **SELECT 필드명 FROM 테이블명**


- 또한, querySet를 사용해서 데이터를 정렬해서 조회해볼 수도 있다. 위의 내용과 이어서..
```terminal
>>> qs = Post.objects.all().order_by('id')
>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", 
"instagram_post"."updated_at" FROM "instagram_post" ORDER BY "instagram_post"."id" ASC

>>> print(qs)
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>

>>> qs = Post.objects.all().order_by('-id')
>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", 
"instagram_post"."updated_at" FROM "instagram_post" ORDER BY "instagram_post"."id" DESC

>>> print(qs)
<QuerySet [<Post: 세번째 메세지>, <Post: 두번째 메세지>, <Post: 첫번째 메세지>]>
```

- 이렇게 id를 기준, 오름차순으로 데이터를 정렬해서 조회할 수 있게 된다. 그리고 .order_by('-id') 이렇게 해주면 내림차순이 된다.

