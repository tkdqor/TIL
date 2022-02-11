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

- **Post.objects.all() 등 이러한 queryset의 실제 쿼리를 보고 싶다면,** 

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

- 이렇게 id를 기준, 오름차순으로 데이터를 정렬해서 조회할 수 있게 된다. 그리고 **.order_by('-id') 이렇게 해주면 내림차순이 된다.**

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
>>> qs  
<QuerySet [<Post: 세번째 메세지>, <Post: 두번째 메세지>]>
```
- 이렇게 입력했을 때 데이터베이스에 쿼리를 진행하게 된다.(또는 print(qs)) 그래서 실제로 쿼리를 진행한 것이니 결과를 출력해주는 것이다. 
  - 이러한 쿼리는 우리가 데이터가 필요하거나, 출력이 필요하거나 리스트 변환을 한다던지 실제 데이터가 필요할 때 최대한 lazy하게 동작을 하게 된다.

- django는 데이터베이스 의존적인 애플리케이션이기 때문에, 대개 병목현상이 성능 관련해서 IO에서 발생하게 된다. 근데 input/output IO가 대부분 파일 접근보다는 데이터베이스 IO 접근이기 때문에 QuerySet과 데이터베이스 쿼리에 대해서 명확하게 잘 이해하는 것이 django 성능 향상에 가장 중요한 항목이다.


### QuerySet은 Chaining을 지원
- 즉, Post.objects.all().flter(..) 나 Post.objects.all().exclude(..) 와 같은 queryset은 원래의 있는 queryset객체에 변화를 가하는 게 아니라, .flter()를 사용하면 새로운 queryset를 만들어내고 / .exclude()를 사용하면 또 새로운 queryset를 만들어 낸다.

```terminal
>>> qs = Post.objects.all().filter(message='첫번째 메세지')

>>> qs = Post.objects.all().filter(message__icontains='첫번째')

>>> qs = Post.objects.all().filter(message__startswith='첫번째')
```

- 첫번째처럼 qs를 정해주면, 정확하게 message필드가 해당 문자열인 값을 조회하는 것이다. 그리고 두번째 qs는, message필드의 데이터 중 '첫번째' 라는 문자열이 포함된 값을 조회하게 된다. 세번째 qs는 '첫번째'라는 문자열로 시작하는 데이터를 조회한다. 이렇게 여러가지 기능들이 있다.
  - **이렇게 filter나 exclude에서는, django에서 조건을 줄 때 -> .filter(필드명__관련된operation='..') 이러한 구조로 쓴다. 이 operation은 각 필드 별 타입에 따라서 지원되는 operation이 달라진다.**
  - 이러한 내용과 관련해서 모델 필드에 대한 django 공식 문서를 꼭 찾아보자.

```terminal
>>> query = '첫번째'     # 검색어
>>> qs = Post.objects.all().filter(message__icontains=query)
>>> qs
<QuerySet [<Post: 첫번째 메세지>]>

>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", "instagram_post"."updated_at" FROM "instagram_post" WHERE "instagram_post"."message" LIKE %첫번째% ESCAPE '\'
```

- 그리고 이런식으로 특정 문자열을 포함하고 있는 변수를 설정해서 필터 기능을 사용할 수 있다. 그래서 '첫번째' 라는 문자열이 포함된 데이터가 출력된다.


### QuerySet에서 주의할 점은, chaining 줄이 너무 길어진다고 해서 enter로 다음 줄로 넘기면 안된다.
```terminal
qs = Post.objects.all()
      .filter(message__icontains=query).order_by('-id')
print(qs.query)
```

- 이러면 python은 IndentationError가 발생하게 된다. **그래서 다음 줄로 넘기고 싶을 때는, 넘기기 직전에 Escaping 처리, 즉 Escape code \을 붙여주고 enter를 누르면 넘겨진다.**
```terminal
>>> query = '메세지'
>>> qs = Post.objects.all()\
... .filter(message__icontains=query)\
... .order_by('-id')

>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", "instagram_post"."updated_at" FROM "instagram_post" WHERE "instagram_post"."message" LIKE %메세지% ESCAPE '\' ORDER BY "instagram_post"."id" DESC

>>> qs
<QuerySet [<Post: 세번째 메세지>, <Post: 두번째 메세지>, <Post: 첫번째 메세지>]>
```

- **QuerySet이 너무 길 때, 다음과 같은 방법으로도 해볼 수 있다.**
```terminal
>>> query = '메세지'
>>> qs = Post.objects.all()
>>> qs = qs.filter(message__icontains=query)
>>> qs = qs.order_by('-id')

>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", "instagram_post"."updated_at" FROM "instagram_post" WHERE "instagram_post"."message" LIKE %메세지% ESCAPE '\' ORDER BY "instagram_post"."id" DESC

>>> qs
<QuerySet [<Post: 세번째 메세지>, <Post: 두번째 메세지>, <Post: 첫번째 메세지>]>
```


### QuerySet 다양한 조회 요청 방법 -> SELECT SQL 생성
- queryset.filter() / queryset.exclude() -> exclude()는 포함되지 않는 조건을 의미(filter와 반대)
```terminal
>>> qs = Post.objects.all().exclude(message__icontains='첫번째')

>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", "instagram_post"."updated_at" FROM "instagram_post" WHERE NOT ("instagram_post"."message" LIKE %첫번째% ESCAPE '\')

>>> qs
<QuerySet [<Post: 두번째 메세지>, <Post: 세번째 메세지>]>
```

- **특정 모델객체 1개에 접근할 때**
  - queryset[숫자인덱스]   -> 근데 만약 해당 queryset의 항목이 3개라면, 인데스로 접근할 수 있는게 0,1,2가 된다. -> 0,1,2로 접근한다면 모델 객체를 반환한다.
  - queryset.get(...)   -> 있다면 모델객체 반환, 없다면 DoesNotExist 에러 발생 / 만약 해당 조건이 2개 이상의 대해서 요청하는 조건이라면 MultipleObjectsReturned 라는 예외가 발생.
  - queryset.first()    -> 해당 항목에서 첫번째를 가져오게 됨. 없다면 None 반환.
  - queryset.last()     -> 해당 항목에서 마지막을 가져오게 됨. 없다면 None 반환.
    - first()와 last()는 에러, 예외가 발생하는 게 아니라 None를 반환해주기 때문에 없을때에 대한 조건을 체크하기 쉽다.

```terminal
>>> qs = Post.objects.all()
>>> qs[1]
<Post: 두번째 메세지>

>>> qs.get()
instagram.models.Post.MultipleObjectsReturned: get() returned more than one Post -- it returned 3!    # MultipleObjectsReturned 에러 발생

>>> qs.get(pk=1)
<Post: 첫번째 메세지>

>>> qs.get(id__lte=2)     # id가 2보다 작거나 같다
instagram.models.Post.MultipleObjectsReturned: get() returned more than one Post -- it returned 2!

>>> qs.first()
<Post: 첫번째 메세지>

>>> qs.last()
<Post: 세번째 메세지>

>>> qs.none()
<QuerySet []>
```



- less than ~보다 작다 / less than equal ~보다 작거나 같다 / greater than ~보다 크다 / greater than equal ~보다 크거나 같다
  - lt / lte / gt / gte 이렇게 django queryset에서 숫자나 대소비교가 가능한 time이나 date 등에서 사용할 수 있다.

- qs.none()은 아무것도 지정하지 않는 queryset를 만드는데, 이 자체가 빈 queryset이 된다.


### filter와 exclude는 반대 조건 -> SELECT 쿼리에 WHERE 조건 추가
- 인자로 "필드명 = 조건값"을 지정해야 함.
- 1개 이상의 인자는 모두 and 조건으로 묶인다.
  - OR 조건으로 묶으려면, django.db.models의 Q라는 객체 활용

```terminal
>>> Item.objects.filter(name="New item", price=3000)  # 이름과 가격이 조건에 맞는 값을 찾겠다
>>> Item.objects.exclude(name="New item", price=3000) # 이름과 가격이 조건으로 아닌 값을 찾겠다
```

```terminal
>>> from django.db.models import Q

>>> qs = Post.objects.all().filter(id__gte=2, message__icontains='메세지')  # 아이디가 2보다 크거나 같고 '메세지'라는 메세지를 포함하는 데이터 조회 (AND 조건)
>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", "instagram_post"."updated_at" FROM "instagram_post" WHERE ("instagram_post"."id" >= 2 AND "instagram_post"."message" LIKE %메세지% ESCAPE '\')
```

- 이렇게만 하면 filter의 조건을 and로 묶는 것이지만,

```terminal
>>> from django.db.models import Q

>>> qs = Post.objects.all().filter(Q(id__gte=2) & Q(message__icontains='메세지'))

>>> qs = Post.objects.all().filter(Q(id__gte=2) | Q(message__icontains='메세지'))
>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", "instagram_post"."updated_at" FROM "instagram_post" WHERE ("instagram_post"."id" >= 2 OR "instagram_post"."message" LIKE %메세지% ESCAPE '\')
```

- &는 비트연산이다. 이렇게 개별 조건마다 Q로 묶을 수 있다. Q 끼리는 비트연산이 가능하다. Q(..) & Q(..) 이렇게 하면 두 조건을 and로 묶는 것이다. 
- |라는 OR 비트연산자를 사용하면 Q 조건끼리 or로 묶을 수 있다. 

- 또한, 별도의 Q 객체를 따로 뽑아서 사용할 수도 있다.
```terminal
>>> qs = Post.objects.all()

>>> cond = Q(id__gte=2) | Q(message__icontains='메세지')     # cond는 condition, 조건의 약자

>>> qs = qs.filter(cond)

>>> print(qs.query)
SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."photo", "instagram_post"."is_public", "instagram_post"."created_at", "instagram_post"."updated_at" FROM "instagram_post" WHERE ("instagram_post"."id" >= 2 OR "instagram_post"."message" LIKE %메세지% ESCAPE '\')
```


### 필드타입 별, 다양한 조건 매칭
- 데이터베이스에 따라 생성되는 SQL이 다르다.

- **숫자/날짜/시간 필드** 
  - 필드명__lt = 조건값 -> 필드명 < 조건값
  - 필드명__lte = 조건값 -> 필드명 <= 조건값
  - 필드명__gt = 조건값 -> 필드명 > 조건값
  - 필드명__gte = 조건값 -> 필드명 >= 조건값


- **문자열 필드**
  - 필드명__startswith = 조건값 -> 필드명 LIKE "조건값%"
  - 필드명__istartswith = 조건값 -> 필드명 ILIKE "조건값%"
  - 필드명__endswith = 조건값 -> 필드명 LIKE "%조건값"
  - 필드명__iendswith = 조건값 -> 필드명 ILIKE "%조건값"
  - 필드명__contains = 조건값 -> 필드명 LIKE "%조건값%"
  - 필드명__icontains = 조건값 -> 필드명 ILIKE "%조건값%"
    - i는 ignorecase를 의미하여 대소문자를 구분하지 않겠다는 것.

