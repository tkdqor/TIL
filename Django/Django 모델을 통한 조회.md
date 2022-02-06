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

- 꼭 queryset를 사용할 때는 이렇게 정렬을 지정해주는 것이 좋다. order_by가 지정되지 않으면 데이터베이스 상황에 따라서 정렬되지 않은 항목들이 나올 수 있기 때문이다. 그래서 기본적으로 정렬을 지정해주자.
  - 또한, Post.objects.all().order_by('-id', 'message')와 같이 정렬 기준을 콤마로 여러 개 줘서 정렬할 수도 있다. 다만, 기준을 많이 설정하기 보다는 1~2개 정도로만 설정하는 게 권장된다.
  - 그리고 [:10] 이런식으로 슬라이싱, 범위를 넣어줄 수 있다.

```terminal
>>> qs = Post.objects.all().order_by('-id')[:2]

>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", "instagram_post"."updated_at" FROM "instagram_post" ORDER BY "instagram_post"."id" DESC LIMIT 2

>>> print(qs)
<QuerySet [<Post: 세번째 메세지>, <Post: 두번째 메세지>]>
```

- 이렇게 처음 2개만 조회하는 [:2] 슬라이싱을 사용하면, sql문에 LIMIT 2가 추가되어 처음 2개만 조회가 된다.

* * *
## QuerySet
- QuerySet은 SQL를 생성해주는 인터페이스이다. 
- 순회가능한 객체이기 때문에 우리가 for loop에서 사용할 수 있다.
- Model Manager를 통해 -> 해당 Model에 대한 QuerySet를 획득한다.
  - Post.objects.all() 코드는 "SELECT * FROM post..."
  - Post.objects.create(...) 코드는 "INSERT INTO..."

```terminal
>>> for post in qs:
...     print(post)
... 
세번째 메세지
두번째 메세지
```

- queryset이 순회가능한 객체이기 때문에, 이렇게 shell에서 for문을 사용해볼 수 있다. 여기서 post는 Post모델에 대한 인스턴스가 된다. 그래서 다음과 같이
```terminal
>>> for post in qs:
...     print(post.id, post.message, post.created_at)
... 
3 세번째 메세지 2022-02-03 11:20:06.877463+00:00
2 두번째 메세지 2022-02-03 11:19:55.685392+00:00
```

- 이런식으로 Post 모델에 접근할 수 있다. 그리고 
```terminal
>>> for post in qs:
...     print(post.id, post.message, post.created_at)
...     print(post.__dict__)
... 
3 세번째 메세지 2022-02-03 11:20:06.877463+00:00
{'_state': <django.db.models.base.ModelState object at 0x1061822e0>, 'id': 3, 'message': '세번째 메세지', 'photo': 'instagram/post/2022/02/05/barella2.jpeg', 'is_public': True, 'created_at': datetime.datetime(2022, 2, 3, 11, 20, 6, 877463, tzinfo=<UTC>), 'updated_at': datetime.datetime(2022, 2, 5, 7, 24, 38, 585393, tzinfo=<UTC>)}
2 두번째 메세지 2022-02-03 11:19:55.685392+00:00
{'_state': <django.db.models.base.ModelState object at 0x106182490>, 'id': 2, 'message': '두번째 메세지', 'photo': '', 'is_public': True, 'created_at': datetime.datetime(2022, 2, 3, 11, 19, 55, 685392, tzinfo=<UTC>), 'updated_at': datetime.datetime(2022, 2, 4, 9, 28, 19, 504548, tzinfo=<UTC>)}
```

- print(post.던더dict던더)를 추가해주면, 사전, 딕셔너리가 나오게 된다. 더 나아가서 

```terminal
>>> for post in qs:
...     print("id: {id}, message: {message} {created_at}".format(**post.__dict__))
... 
id: 3, message: 세번째 메세지 2022-02-03 11:20:06.877463+00:00
id: 2, message: 두번째 메세지 2022-02-03 11:19:55.685392+00:00
```

- 이렇게 다른 형식으로도 작성해볼 수 있다.



### QuerySet의 특징 중의 하나는, Lazy하다는 것이다.
- 즉, 이렇게 qs = Post.objects.all() 해서 QuerySet 객체를 만들자마자 데이터베이스에 select 쿼리를 하지 않는다. 데이터를 쿼리할 준비만 하고 있는 것이다. 
  - 이걸 이어서 qs = Post.objects.all().order_by('-id')[:2] 이렇게 queryset를 새롭게 객체를 만들고, 조건을 계속해서 추가하더라도 지금까지는 데이터베이스의 쿼리를 하지 않고 있는 것이다.
  - 실제로는 terminal에서 
```terminal
qs 
<QuerySet [<Post: 세번째 메세지>, <Post: 두번째 메세지>]>
```
- 이렇게 입력했을 때 데이터베이스에 쿼리를 진행하게 된다. 그래서 실제로 쿼리를 진행한 것이니 결과를 출력해주는 것이다. 
  - 이러한 쿼리는 우리가 데이터가 필요하거나, 출력이 필요하거나 리스트 변환을 한다던지 실제 데이터가 필요할 때 최대한 lazy하게 동작을 하게 된다.
