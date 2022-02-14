## 데이터베이스 id column
- 우리가 생성한 데이터베이스는 기본적으로 맨 앞에 id라는 column이 자동으로 추가된다. 이러한 id는 데이터를 구분하는 key가 된다.
  - 이 id column은 우리가 별도로 지정하지 않아도 알아서 자동으로 추가된다. 게시물이 추가될 때마다 그 값이 자동으로 증가한다.
  - 그리고 이 id로 우리가 찾고자 하는 데이터를 빠르게 찾아낼 수 있다.

- **RDB에서 데이터를 조회하기 위해 가장 사용하기 좋은 식별자를 Primary Key라고 부른다. 이 id가 Primary Key에 해당한다.**


## python 코드로 데이터 관리하기
```terminal
python manage.py shell
```

- **terminal에 위와같이 입력하면 -> django의 각종 기능을 테스트해볼 수 있는 환경이 열리는 django shell을 사용할 수 있다.**
  - 참고로 terminal에 python3 이렇게 입력해도 **REPL(레플)** 이라는 기능을 활용할 수 있다. >>>가 출력되면서 python 코드를 입력하고 계산 값을 바로 확인할 수 있다.
  - **django shell은 REPL과 유사하나, django의 설정 파일인 settings.py을 비롯해서 각종 django의 기능들을 활성화한 상태에서 실행되기 때문에, django에서만 사용가능한 다양한 리소스와 기능들을 사용할 수 
    있다는 점이 다르다.**
    
- django shell에서 Post model 클래스를 활용해 데이터를 직접 생성해보자.

```terminal
>>> from posts.models import Post
>>> Post.objects.all()
<QuerySet [<Post: 보라돌이: 보라보라: 2022-02-14 08:54:10.887019+00:00>, <Post: 뚜비: 뚜비뚜비: 2022-02-14 08:57:27.698818+00:00>, <Post: 나나: 나나나나: 2022-02-14 08:59:08.763715+00:00>, <Post: 뽀: 뽀뽀뽀뽀: 2022-02-14 08:59:15.172612+00:00>]>

```

- **모든 모델 클래스에는 objects라고 불리는 model manager라는 게 있다. 이 model manager 안에는 데이터베이스를 조작할 수 있는 각종 쿼리 기능들이 존재한다.**
  - Query는 해석하다, 질의하다의 뜻. 즉, 데이터베이스한테 데이터를 조회하고 생성하기 위해 요청하는 것이다.

- Post.objects.all() queryset은 -> 데이터베이스로부터 Post 테이블 내부에 있는 모든 데이터를 불러오는 하나의 기능이다.
- **QuerySet은 django manager를 통해서 데이터베이스로부터 가져온 데이터의 묶음을 의미한다. 데이터베이스 테이블에 저장되어 있었던 각각의 행, 각각의 row가 -> model 클래스의 인스턴스로 변환이 되어서 
  묶음으로 전달이 된 것이다.**
  
  
### python django shell로 게시물 생성
- 작성일자를 채워주기 위해서
```terminal
>>> from django.utils import timezone

>>> new_post = Post()

>>> new_post
<Post: : : None>
```

- 먼저 new_post라는 변수에다가 Post 클래스를 생성자로 활용해서 Post() 이렇게 내용물이 없는 객체를 만들어보자. 그 상태에서 new_post를 입력하면 비어있는 Post 클래스 인스턴스만 출력하게 된다.
  - cf) 지금 repl 환경이기 때문에 print(new_post) 가 아니라, new_post라고만 해도 출력이 되는 것이다.
  
- 이제, new_post라는 Post 모델 객체에다가 필드에 데이터를 채워보자.  
```terminal
>>> new_post.author = 'sangbaek'
>>> new_post.body = 'My new Post'
>>> new_post.created_at = timezone.now()

>>> new_post
<Post: sangbaek: My new Post: 2022-02-14 13:52:48.483342+00:00>
```

- 위의 코드처럼 new_post라는 객체의 필드에 값을 설정해주었지만, 지금까지는 우리가 이런 게시물을 만들고 싶다는 인스턴스만 만든 것이지, 이 인스턴스가 실제로 데이터베이스에 저장이 되는 건 아니다. 아직 데이터베이스에 반영이 되지 않았다.
  - model 클래스의 인스턴스를 활용해서 추가하고자 하는 새로운 데이터를 만들고 이 데이터를 최종적으로는

```terminal
>>> new_post.save()

>>> new_post.id
5
```

- 이렇게 save 함수를 실행해줘야만, 새로운 데이터를 생성하기 위한 SQL 쿼리가 내부적으로 실행되면서 데이터베이스에 우리가 원하는 데이터가 추가된다.
- 그 이후에 new_post.id를 조회해보면 새롭게 추가된 것을 확인할 수 있다.


### python django shell로 게시물 생성 (2)
- 위의 코드처럼 필드값을 하나하나 정하지 말고 한 줄로 만들어 보기.

```terminal
>>> new_post2 = Post(author='sangbaek', body='My second Post')
>>> new_post2.save()
```

- 이런식으로 한 줄에 우리가 원하는 게시물 데이터를 생성할 수 있다. 실제 데이터베이스 반영을 위해 new_post2.save() 까지 입력하자.
  - **DateTimeField에 auto_now_add=True라고 설정했기 때문에, created_at를 따로 설정하지 않아도 된다.**
    - **다만, settings.py에서 USE_TZ = True로 되어있을 경우에는 -> DB에서 created_at이 UTC 기준이 된다. True면 템플릿이나 폼에서만 TIME_ZONE에 설정한 시간으로 보여준다.**
    - **만약, USE_TZ = False로 바꾸고 새롭게 데이터를 생성하면 -> DB에서 created_at이 TIME_ZONE = 'Asia/Seoul'로 기준이 변경된다. False여야 장고의 모든 시간대가 TIME_ZONE에 설정한 시간으로 저장된다.**
    - 관련 블로그 : https://velog.io/@sawol/Django-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0-1, https://grape-blog.tistory.com/18, https://computer-science-student.tistory.com/230


### python django shell로 데이터 조회
```terminal
>>> Post.objects.get(id=1)
<Post: 보라돌이: 보라보라: 2022-02-14 08:54:10.887019>
```

- **objects.get()를 활용해서 특정 id를 가지고 있는 데이터를 조회할 수 있다. 데이터 1건을 가져올 때는 이 objects.get()를 활용하면 된다.**
  - 데이터의 단 건이기 때문에 데이터의 묶음인 QuerySet이 아닌 Post 모델의 객체 정보가 출력된다.
  - 만약, 데이터가 없다면 에러가 발생한다.


- **데이터 단 건 조회가 아닌, 해당 조건을 만족하는 모든 데이터를 조회하려면 -> objects.filter() 함수를 사용하자.** 
  - 해당 조건을 만족하는 모든 데이터들을 뽑아낼 수가 있다.

```terminal
>>> Post.objects.filter(author='sangbaek')
<QuerySet [<Post: sangbaek: My new Post: 2022-02-14 14:00:52.534588>, <Post: sangbaek: My second Post: 2022-02-14 23:43:58.840950>]>

>>> Post.objects.filter(author="sa")
<QuerySet []>
```

- 이렇게 작성자에 대한 조건을 주면, 해당 조건을 만족하는 데이터를 QuerySet의 형태로 전달받을 수 있다.
- 이 filter 함수는 해당 조건을 만족하는 데이터가 아예 없거나, 혹은 오직 하나만 있는 경우에도 -> 에러가 아니라, 무조건 결과는 QuerySet으로 전달해준다. 하나만 있더라도 하나만 있는 QuerySet를 반환한다.


### python django shell로 데이터 수정
- 만약, 5번 게시물을 수정하고자 한다면

```terminal
>>> post = Post.objects.get(id=5)
>>> post.body = 'My edited Post' 

>>> post.save()

>>> Post.objects.all()
 <QuerySet [<Post: 보라돌이: 보라보라: 2022-02-14 08:54:10.887019>, <Post: 뚜비: 뚜비뚜비: 2022-02-14 08:57:27.698818>, <Post: 나나: 나나나나: 2022-02-14 08:59:08.763715>, <Post: 뽀: 뽀뽀뽀뽀: 2022-02-14 08:59:15.172612>, <Post: sangbaek: My edited Post: 2022-02-14 14:00:52.534588>, <Post: sangbaek: My second Post: 2022-02-14 23:43:58.840950>]>
``` 


- **post.body = 'My edited Post' 이렇게 새롭게 데이터를 설정한다고 해서 끝나는 것이 아니라 -> 실제로 데이터베이스에 반영하기 위해서는 post.save() 라는 함수를 사용해야 한다.**
  - 실제로 바뀌었는지 확인하려면, Post.objects.all()로 데이터를 확인해보자. 5번 게시물의 내용이 바뀐 것을 확인할 수 있다.


### python django shell로 데이터 삭제
- delete() 함수를 호출하면 삭제가 된다.
- 만약, 5번 게시물을 지운다고 한다면

```terminal
>>> post = Post.objects.get(id=5)

>>> post.delete()
(1, {'posts.Post': 1})
```

- **delete() 함수를 호출하면 데이터가 바로 삭제 된다. posts 패키지 내부에 있는 Post 모델 클래스가 지워졌다고 나온다.**

* * *

### View 로직 구성하기
- **여기까지 python django shell로 데이터 CRUD를 모두 진행했다.**
- **그리고 우리가 작성해봤던 모델 데이터 CRUD 관련 queryset를 View 함수에서도 작성할 수 있다.**
  - 특히, 데이터가 많을 때에는 objects.all()로 모든 데이터를 가져오기 보다는 -> **.order_by 함수를 사용해서 데이터를 정렬해서 보여주는 게 좋다.**

```python
def index(request):
    posts = Post.objects.all().order_by('-created_at')

...
```

- .order_by() 함수에다가 괄호에는 정렬의 기준이 되는 필드, column를 지정해주면 된다.
  - ex) 게시물이 작성된 시간 순서로 정렬하고자 한다면, posts = Post.objects.all().order_by('created_at') 이렇게 지정해주면 된다.
  - 기본적으로 .order_by 함수는 오름차순을 기준으로 정렬해준다. 만약 내림차순으로 정렬하고 싶다면 -> posts = Post.objects.all().order_by('-created_at') 이렇게 필드 이름 앞에다가 -를 붙이면 된다.
  

- 또한, 데이터의 개수를 제한해서 가져오고 싶을 수도 있다. 이 때는 문자열 슬라이싱을 하는 것처럼, 대괄호를 쓰고 콜론을 적은 다음 슬라이싱 하듯이 인덱스를 적어주면 된다.
```python
def index(request):
    posts = Post.objects.all().order_by('-created_at')[:3] 

...
```

- 내림차순으로 정렬이 된 상태에서, 맨 앞에서부터 총 3개의 데이터를 추출해서 보고싶다고 했을 때는 위와 같이 적어주면 된다.
  - 그러면, 최신 순서대로 3개의 데이터를 보여주게 된다.


- 만약, 현재 데이터베이스에 정말 많은 데이터가 저장되어있다고 가정해보자. 그런 상황에서 posts = Post.objects.all() 이라고 작성하면, 용량이 큰 데이터가 전부 django server로 넘어와서 posts라는 변수에 저장이 되버린다.
  - 그 다음, **posts = Post.objects.all().order_by('-created_at')[:3]** 이러한 코드를 작성하게 되면 -> 데이터베이스로부터 많은 양의 데이터를 일단 django server에 가져온 다음에, 3개를 가져오는 것이 아니라 / django 모델 클래스에서 사용하는 슬라이싱의 경우에는, 리스트에서 슬라이싱을 하는 코드가 아니다. 즉, SQL 코드 상에서 3개를 짤라서 가져오라고 하는 코드로 변환이 된다. 
  - **결론적으로, 데이터베이스에 데이터가 얼마나 있든 상관없이 실제로 네트워크 상으로 통신할 때에는 모든 데이터를 가져와서 자르는 것이 아니라 / 데이터베이스 내부에서 정렬을 하고, 거기서 3개를 추출하기 때문에 -> 실제로 우리가 전달받는 데이터는 3개만 받게 되는 것이다.**
  - 이러한 과정 덕분에 응답 속도가 훨씬 빠르다.

- 만약, 실제로 posts = Post.objects.all().order_by('-created_at')[:3]와 같은 코드가 SQL로 변환되면 어떤 코드로 작성되는지 궁금하다면 터미널에 로그를 남겨보면 된다.
```python
def index(request):
    posts = Post.objects.all().order_by('-created_at')[:3]    # Post 모델 테이블 전체 데이터에서 정렬해서 가져오기
    print(posts.query)
    
...
```

- Post.objects와 같이 model 클래스의 manager를 통해서 가져온 데이터에는 .query 라는 것으로 그 데이터를 가져오기 위해서 어떠한 SQL 코드가 실행되었는지를 확인할 수 있다.

```terminal
SELECT "posts_post"."id", "posts_post"."author", "posts_post"."body", "posts_post"."created_at" FROM "posts_post" ORDER BY "posts_post"."created_at" DESC LIMIT 3
```

- 실제로 View 함수를 호출하기 위해 새로고침을 해보면, 위와 같은 SQL문을 확인할 수 있다. LIMIT 3은 SQL 코드로 3개만 잘라서 가져오겠다는 코드이다.
  - 이 sql 코드가 실제로 데이터베이스에 수행이 되었고 결과를 우리가 받은 것이다.
