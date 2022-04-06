## QuerySet API로 Model 수정 및 삭제
- 먼저, 이전까지 작업했던 liongram 디렉터리 - posts 디렉터리 - models.py를 다시 확인해보자.

- **그리고, python 인터프리터의 경로를 직접 찾아서 확인해볼 수 있다.**
  - 루트 디렉터리 내부에 venv 디렉터리 -> bin 디렉터리 -> python 파일을 선택해주면 된다. (맥북은 python3 일 것이다)

- **그 다음, liongram 위치에서 django shell를 들어가보자.**

```terminal
python manage.py shell
```

- **사실 이 터미널에서 그냥 python이라고만 입력해도 python 인터프리터가 실행되지만, 그렇게 해서는 from models 이렇게 사용할 수 없다. 위의 코드를 입력해서 인터프리터를 실행하면 django 변수들을 가져와서 실행하기 때문에 from posts.models 이렇게 사용할 수 있다.**


### django Shell 사용하기
- 위와 같이 코드를 입력하고 난 다음에, posts App의 models.py에 있는 모델 데이터에 접근할 수 있다.

```terminal
>>> from posts.models import Post
>>> from posts.models import User
>>> from posts.models import Comment
```

- 이런식으로 가져올 수 있다. 이제 Post 모델에 데이터를 하나 생성해보자.
- **그 전에, objects 라는 것은 --> 우리가 QuerySet API를 사용하기 위한 객체라고 보면 된다.**

```terminal
>>> Post.objects.create(content='첫번째 글 작성입니다. 쿼리셋 API 사용')
<Post: Post object (4)>
```

- **이런식으로 데이터를 계속 생성할 수 있다. 이렇게 create API는 생성된 데이터 하나만 return 해준다.**
- 그리고나서 전체를 다 가져와보면,

```terminal
>>> Post.objects.all()
<QuerySet [<Post: Post object (1)>, <Post: Post object (2)>, <Post: Post object (3)>, <Post: Post object (4)>, <Post: Post object (5)>]>
```

- 이렇게 데이터의 QuerySet이 출력된다. 이걸 for문으로 하나씩 뽑아볼수도 있다.

```terminal
>>> for post in Post.objects.all():
...     print(post)
... 
Post object (1)
Post object (2)
Post object (3)
Post object (4)
Post object (5)
```

- 또는, 아래와 같이 출력해볼 수도 있다.

```terminal
>>> for post in Post.objects.all():
...     print(f'{post.created_at} - {post.content}')
... 
2022-04-01 16:27:00.968765+00:00 - Shell를 통한 데이터 생성
2022-04-01 16:28:15.530918+00:00 - Shell를 통한 데이터 생성11
2022-04-01 16:28:25.067829+00:00 - Shell를 통한 데이터 생성23
2022-04-06 06:40:47.530134+00:00 - 첫번째 글 작성입니다. 쿼리셋 API 사용
2022-04-06 06:42:18.865572+00:00 - 쿼리셋 실습 중!
```

* * *
- **이번에는 count() API를 사용해보자.**

```terminal
>>> Post.objects.all().count()
5
```

- 위와 같이 데이터의 총 개수를 파악해볼 수 있고 / 게시판의 content가 'Shell를 통한 데이터 생성' 이것과 똑같은 데이터가 몇 개인지도 확인할 수 있다.

```terminal
>>> Post.objects.filter(content='Shell를 통한 데이터 생성')
<QuerySet [<Post: Post object (1)>]>
```

- filter를 사용해서 해당 텍스트와 동일한 것만 출력해준다.
