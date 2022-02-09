## ManyToManyField
- M:N 관계에서 어느쪽이라도 필드 지정 가능
- 공식문서 : https://docs.djangoproject.com/en/4.0/ref/models/fields/#manytomanyfield


- ManyToManyField(to, blank=False)
  - 첫번째 인자로는 어떠한 모델에 대해 관계를 설정할 것인지 입력. on_delete 옵션은 없고 blank라는 옵션이 유의미하게 사용된다.

```python
class Post(models.Model):
    tag_set = models.ManyToManyField('Tag', blank=True)
    
    
class Tag(models.Model):
    name = models.CharField(max_length=100, unique=True)
```

- 이렇게 Post:Tag 모델이 M:N 관계라서 Post 모델에 관계를 지정해주었다.


### ManyToManyField 예시
- instagram App의 models.py 설정
```python
class Post(models.Model):       
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    message = models.TextField()    
    photo = models.ImageField(blank=True, upload_to='instagram/post/%Y/%m/%d') 
    is_public = models.BooleanField(default=False, verbose_name='공개여부')      
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
    tag_set = models.ManyToManyField('Tag', blank=True)          # tag_set = models.ManyToManyField(Tag) 이 코드는 에러


class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)   # 해당 Tag의 name은 하나가 생성이 되면, Tag 테이블 내에서 유니크함을 보장받도록 하는 것이 맞다는 판단으로 설정
    # post_set = models.ManyToManyField(Post)  이렇게 지정해도 된다.
   
    def __str__(self):
        return self.name
    
    
# Tag라는 모델, 대상이 여러 모델에 의해서 참조될 수 있기 때문에 -> Tag에 M:N 관계를 설정하는 것 보다는, Tag를 활용하는 모델에다가 설정하는 것이 더 의미에 맞다. 
```

- M:N관계를 Post 또는 Tag 모델 모두 설정할 수 있다. 그런데 Post에서 설정할 때는, tag_set = models.ManyToManyField(Tag) 이렇게만 적으면 Tag 부분이 오류가 난다.
  - python에서는 모든 부분이 다 코드영역이다. 그리고 python은 컴파일러(compiler)를 사용하는 C언어나 자바와는 달리 **인터프리터에 의해 실행되는 스크립트 언어**이다. 그래서 모든 코드들을 직접 실행해서 인지를 하게 된다. 
  - 그래서 tag_set = models.ManyToManyField(Tag) 이 부분이 실행이 될 때, Tag라는 대상을 참조해야 하는데 Tag가 밑에 있기 때문에 이 코드가 실행될 타이밍에는 Tag가 정의되지 않아 문제가 되는 것이다. 따라서 위의 코드처럼 문자열로 처리를 해주면 된다. 

- 또한, 기본적으로 blank=False가 보통이지만, ManyToManyField에서는 blank=True가 의미가 있다.
  - 위의 코드처럼, Post 글 하나를 작성할 때 tag를 지정하지 않고 글을 작성할 수도 있다. Tag가 없는 상황도 만들 수 있도록 허용한다면 blank=True를 해주면 된다. 
  - 만약 blank를 넣지 않는다면 -> django Form에서 유효성 검사를 수행할 때 Tag를 최소 1개라도 지정안했다고 유효성 검사가 실패하게 된다.


- 인터프리터(interpreter)란 소스 코드를 처음부터 한 라인씩 차례대로 해석하며 실행하는 프로그램이다. 
  - 인터프리터는 실행할 때마다 코드를 한 라인씩 바로 기계어로 변환하기 때문에 실행 속도가 컴파일러 언어에 비해 다소 느리지만, 코드가 완전히 작성되지 않아도 작성된 부분까지만을 테스트할 수 있다. 또한, 대화형 기능을 통해 사용자가 곧바로 코드를 수정할 수 있다.

```terminal
python manage.py makemigrations instagram
python manage.py migrate instagram
```
- 이렇게 models.py를 수정하고 DB에 반영하자.

* * *

### DB browser for SQLite 사용하기
- 우리가 지금은 sqlite라는 데이터베이스를 활용하고 있는데, 이 sqlite를 편하게 관리하기 위해 사용하는 DB Tool이 바로 DB browser for SQLite가 있다.
- https://sqlitebrowser.org/ 여기에서 다운받아보자.
  - 설치가 되었으면, 데이터베이스 열기 -> 우리 프로젝트 디렉터리 내부에 있는 db.splite3 파일을 선택해서 열여보자. 그러면 모델 테이블 내역을 볼 수 있다.

- Post와 Tag 모델을 M:N 관계로 설정하고, Post 모델에다가 tag_set이라는 필드이름으로 ManyToManyField 이렇게 설정했었는데 -> DB browser를 확인해보면
  - instagram_post_tag_set 이라는 이름으로 하나의 중간 테이블이 생성된 것을 확인할 수 있다.
  - 그 테이블 안에는, id / post_id / tag_id 라는 column으로 구성되어 있다.
<img src="https://user-images.githubusercontent.com/95380638/153165790-f7f9efe6-391d-4ab3-86b9-317754f467dd.png" width="70%" height="70%">


- ForeignKey(1:N관계)와 OneToOneField(1:1관계)는 2개의 모델 테이블만 있으면 충분한데, ManyToManyField(M:N관계)는 2개의 테이블만으로는 관계를 정의할 수가 없다. M:N관계를 위해서는 중간 테이블이 새롭게 필요하게 된다. 그래서 2개의 모델의 pk값을 column으로 정의한 테이블이 생성된다.
- DB browser를 통해서 봤을 때는 Post 모델에 M:N관계를 설정했지만 Post 모델에는 새로운 필드가 생기지 않았음을 확인할 수 있다.

* * *

### Tag 모델 admin.py에 등록하기
```python
from .models import Post, Comment, Tag 

@admin.register(Tag)
class TagAdmin(admin.ModelAdmin):
    pass
```

- 이렇게 입력하면 admin 페이지에 Tag 모델이 뜨게 된다.


- 그리고, admin 페이지에서 Post 모델의 객체를 하나 클릭해보면 -> Tag set: 이라는 부분이 생성되어 있다. +를 누르면 name이라고 뜨게 되고 여기에 tag 이름을 입력해보자. 

<img width="554" alt="image" src="https://user-images.githubusercontent.com/95380638/153171670-a9bb980e-5b52-484c-9246-141de60abd7b.png">

- 그러면 다음과 같이 입력한 이름이 tag로 지정된다. 따라서 1개의 post에 여러 개의 tag가 담겨져 있을 수 있다. 


### django shell로 확인해보기
```terminal
In [1]: from instagram.models import Post, Tag

In [2]: post = Post.objects.first()

In [3]: post
Out[3]: <Post: 네번째 포스팅>

In [4]: post.tag_set
Out[4]: <django.db.models.fields.related_descriptors.create_forward_many_to_many_manager.<locals>.ManyRelatedManager at 0x10b59b400>

In [5]: post.tag_set.all()
Out[5]: <QuerySet [<Tag: 파이썬>]>



In [6]: tag = Tag.objects.first()

In [7]: tag
Out[7]: <Tag: 파이썬>

In [10]: tag.post_set.all()
Out[10]: <QuerySet [<Post: 네번째 포스팅>]>
```


- post.tag_set.all() -> 조회한 post 객체에 해당하는 pk값으로 tag_set 테이블에 있는 tag_id를 매칭하고, 그 pk값에 해당하는 Tag 모델의 객체를 가져와주는 것이다.
- tag.post_set.all() -> 조회한 tag 객체에 해당하는 pk값으로 tag_set 테이블에 있는 post_id를 매칭하고, **reverse 이기 때문에 _set 을 붙여주고, 그 pk값에 해당하는 Post 모델의 객체를 가져와준다.**


- 그리고 이제 Tag 모델에 새로운 객체 하나를 생성해보자.
```terminal
In [1]: from instagram.models import Post, Tag

In [2]: Tag.objects.create(name="장고")
Out[2]: <Tag: 장고>

In [3]: Tag.objects.create(name="AskCompany")
Out[3]: <Tag: AskCompany>

In [4]: Tag.objects.all()
Out[4]: <QuerySet [<Tag: 파이썬>, <Tag: 장고>, <Tag: AskCompany>]>
```

- 이렇게 Tag 모델에 새로운 객체들을 생성해볼 수 있다. 하지만, Tag 모델에만 객체들을 생성한 것이기 때문에 중간 테이블인 Instagram_post_tag_set 모델에는 여전히 객체가 1개밖에 없다.
```terminal
In [8]: post.tag_set.all()
Out[8]: <QuerySet [<Tag: 파이썬>]>

In [9]: tag = Tag.objects.get(name="장고")

In [10]: post.tag_set.add(tag)

In [11]: post.tag_set.all()
Out[11]: <QuerySet [<Tag: 파이썬>, <Tag: 장고>]>



In [12]: post.tag_set.remove(tag)

In [13]: post.tag_set.all()
Out[13]: <QuerySet [<Tag: 파이썬>]>
```

- In [9,10,11] 을 보면, tag라는 변수에 장고라는 이름의 Tag 모델 객체를 조회하고 / post객체에 중간 테이블인 tag_set에 pk로 접근할 때 이 tag 객체를 추가해달라고 하는 것. / 그래서 post 객체의 pk로 접근하게 되면 2개의 tag가 조회되는 것을 확인할 수 있다.
- 그리고 post.tag_set.remove(tag) 이렇게 remove를 사용하면 삭제도 된다.



