## Django 모델 필드
- 장고 모델은 데이터베이스 쿼리를 대신 생성해주고 실행하고 그 결과를 받아와서 가공해주는 역할의 ORM이다. 데이터베이스가 지원하는 기능을 모델을 통해 사용하는 것이다. 데이터베이스에서 지원하는 필드 타입에 따라 다양한 모델 필드가 지원되며, 특정 데이터베이스를 위한 모델 필드도 있다.
- 장고의 모든 기능을 지원하는 데이터베이스는 PostgreSQL이다. 강력한 위치검색쿼리가 필요하거나, PostgreSQL에서만 지원하는 모델필드가 필요할 경우에 검토해보자.
- 마이크로소프트의 MS-SQL 서버와 연동하고자 한다면, microsoft에서 mssql-django 라이브러리를 직접 개발하여 배포하고 있다.

### 기본 지원되는 모델 필드 타입
- 먼저 django의 공식 문서에 있는 필드에 대한 내용을 한 번 읽어보자. https://docs.djangoproject.com/en/3.2/ref/models/fields/#field-types
- https://brunch.co.kr/@ddangdol/4 해당 글은 한글로 번역된 내용이다.

- primary Key: AutoField, BigAutoField
  - 기본적으로 모든 테이블에 id필드가 숨겨져 있는데, 그 필드가 AutoField이다. 숫자가 1씩 자동으로 증가하는 필드이다. 따라서 해당 필드를 우리는 default로 사용하게 된다.
- 문자열: CharField, TextField, SlugField
- 날짜/시간: DateField, TimeField, DateTimeField, DurationField
  - https://tomining.tistory.com/145 해당 블로그를 참고하면, DateField, DateTimeField의 옵션 중, auto_now=True -> 수정될 때마다 현재날짜로 갱신 / auto_now_add=True -> 최초 저장 시에만 현재날짜를 적용
- 참/거짓: BooleanField, NullBooleanField
- 숫자: IntegerField, SmallIntegerField, PositiveIntegerField, PositiveSmallIntegerField, BigIntegerField, DecimalField, FloatField
- 파일: BinaryField, FileField, ImageField, FilePathField
- 이메일: EmailField
- URL: URLField
- UUID: UUIDField
- 아이피: GenericIPAddressField

### 관계에 대한 필드 타입
- ForeignKey(외래키) -> 1:N 관계
- ManyToManyField  -> M:N 관계
- OneToOneField    -> 1:1 


- 우리가 만약 Posting을 저장하는 테이블을 하나 만들고, Tag를 저장하는 필드를 하나 만든다고 했을 때, Posting 테이블에 있는 레코드와 Tag 테이블에 있는 레코드의 관계를 명시할 수 있다. 위의 3가지의 관계를 지정할 수 있다.
- 그리고 기본 지원되는 필드 이외에, 다양한 커스텀 필드들이 있다.
  - django-model-utils: https://django-model-utils.readthedocs.io/en/latest/
  - 여기서 지원되는 필드들을 사용할 수 있다.


## 모델필드는 -> DB 필드타입을 반영
- DB에서 지원하는 필드들을 반영한다. 데이터베이스의 Varchar라는 필드타입이 있는데 이 필드타입에 매핑되는 장고모델 필드는 -> CharField, SlugField, URLField, EmailField 등이 있다.
- 그리고 python 데이터타입과 데이터베이스 데이터타입을 매핑해놓았다.
  - AutoField -> int
    - 장고 모델 중에 AutoField는 python에서 참조할 때 int가 된다.
  - BinaryField -> bytes
  - BooleanField -> bool
  - CharField / SlugField / URLField / EmailField -> str 
    - 이 4개 필드는 모두 문자열로 참조되는데, 차이점은 디폴트로 적용된 유효성 검사 로직 등의 차이가 있다. CharField는 단순히 지정 길이 안에서의 문자열을 저장 / URLField는 그 문자열이 URL 형식이어야지만 저장이 됨 / EmailField에는 이메일 형식이어야지만 저장이 되도록 설정됨

- 또한, 같은 모델필드라고 할지라도 DB에 따라 다른 타입이 될 수도 있다. DB에 따라 지원하는 기능이 다르다. 
  - 그래서 꼭, DB 엔진이 바뀌면 SQL를 확인하는 습관이 중요하다.


## 자주 쓰이는 필드 공통 옵션
- blank: 장고 단에서 validation(유효성 검사) 시에 empty 허용 여부(디폴트: false)
  - 예를 들어, 문자열의 경우에 빈 문자열을 허용하는 것. 이걸 생략하게 되면 false로 빈 문자열을 허용하지 않는다.
- null(DB옵션): null 허용 여부(디폴트: false)
  - blank와 null의 차이를 알려주는 글 https://django-orm-cookbook-ko.readthedocs.io/en/latest/null_vs_blank.html
  - blank=True는 빈 채로 저장되는 것을 허용 / null=True는 필드의 값 자체가 정보 없음으로 저장되는 것을 허용
- db_index(DB옵션): 인덱스 필드 여부(디폴트: false)
  - 모델에 db_index를 걸려고 한다면 이 옵션을 주고 migration을 하게 되면 적용이 된다. 근데 이미 데이터베이스를 꾸며놓았다면, 그 위에 장고의 이 옵션을 걸어도 쓸일이 없다. 이 옵션은 migration할 때 사용한다.
- default: 디플트 값 지정, 혹은 값을 리턴해줄 함수 지정 / 사용자에게 디폴트값을 제공하고자 할 때
- unique(DB옵션): 현재 테이블 내에서 유일성 여부(디폴트: false)
  - migration 할 때도 사용하고 장고 단에서 유효성검사를 수행할 때도 사용한다.
- choices: select 박스 소스로 사용
- validators: validators를 수행할 함수를 다수 지정
  - 모델 필드에 따라 고유한 validators들이 등록 (Ex. 이메일만 받기)
- verbose_name: 필드 레이블, 미지정 시 필드명이 사용
  - 데이터베이스(DB) 하나의 필드는 -> 레이블(데이터를 분류한 제목)과 데이터로 이루어져 있다. 따라서 verbose_name 옵션을 따로 지정하지 않으면 장고 모델에서 사용한 필드 이름이 그대로 레이블이 된다.
- help_text: 필드 입력 도움말


## 장고 모델 예시
```python
from django.db import models


class Post(models.Model):       # 우리가 원하는 데이터베이스에 저장하고 싶은 내역대로 설계를 해서 사용하면 된다.
    message = models.TextField()    # 기본 default 값이 blank=False이다.
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
```

- 위와 같이 message라는 필드는 TextField이고 아무것도 설정하지 않았기 때문에 default로 blank=False이다. 그래서 message에 값이 없다면, 빈 문자열로 저장한다면 모델과 연동된 장고 form에서 유효성 검사를 수행하는 데, 거기서 실패하게 된다. 근데 그 form를 사용하지 않고 그냥 모델만 활용해서 저장하면 유효성 검사 로직을 타지 않는다. 그래서 그런 경우에는 저장은 된다.

- null과 blank 2개는 가급적으로 false로 설정하자.


```python
from django.conf import settings
from django.db import models


class Profile(models.Model):
    user = models.OneToOneField(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    blog_url = models.URLField(blank=True)
    

class Post(models.Model):       # 우리가 원하는 데이터베이스에 저장하고 싶은 내역대로 설계를 해서 사용하면 된다.
    message = models.TextField()    # 기본 default 값이 blank=False이다.
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
    title = models.CharField(max-length=100, db_index=True)
    slug = models.SlugField(allow_unicode=True, db_index=True)
    desc = models.TextField(blank=True)
    image = models.ImageField(blank=True)
    comment_count = models.PositiveIntegerField(default=0)
    tag_set = models.ManyToManyField('Tag', blank=True)
    is_publish = models.BooleanField(default=True)         # 발행여부를 의미
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)


class Comment(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)  # 댓글의 작성자 정보가 있는데, 이 때 외래키는 작성자를 지정한다.
    post = models.ForeignKey(Post, on_delete=models.CASCADE)  # 1개의 글은 여러개의 댓글이 달릴 수 있으니 Post모델과 1:N 관계 형성
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
 

class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)
```

- 다음과 같이, 1개의 글에 여러개의 댓글이 달릴 수 있는 관계, 즉 1:N의 관계일 경우에는 -> N측에다가 **post = models.ForeignKey(Post, on_delete=models.CASCADE)** 다음과 같이 ForeignKey를 설정해주면 된다. N측에서 1의 관계에 있는 것은 Post다 라고 괄호 안에 설정하는 것이다.
- 그리고 댓글의 작성자 정보가 있는데, 이 때 외래키는 작성자를 지정하게 된다. 1명의 유저가 여러개의 댓글을 달 수 있기 때문에 이 경우도 1:N의 관계가 성립된다. -> 이 경우도 Comment 모델이 N측에 있으니 설정한 것이다.
- Post 모델에서도, 1명의 유저가 여러개의 글을 작성할 수 있기 때문에 외래키를 작성자로 지정하게 되고 1:N의 관계가 성립된다. -> Post 모델이 N측에 있으니 Post 모델에서 설정한다.

- Profile 모델의 경우는, 일단 User 모델은 장고의 OS - AUTH 앱에서 기본적으로 지원하고 있다. 그 User 모델과 Profile모델을 1대1 관계로 설정한 예시이다. **user = models.OneToOneField(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)** 이렇게 코드를 입력하고 지정해주었다.

- SlugField의 경우는, 외국의 뉴욕타임즈나 언론사 시스템에 보면, 특정 기사에 대한 url이 영문과 숫자 그리고 하이푼으로 이루어진 경우가 있다. 그런 부분을 slug라고 한다. 그래서 이러한 SlugField를 구성하게 되면 제목과는 다른, 제목으로부터 slug를 자동 생성하는 장고 어드민의 기능이 실행된다.
  - allow_unicode가 원래 false가 디폴트인데 이걸 True로 하면 -> 한글도 지정할 수 있게 된다.

- desc는 description의 약자로 "설명" 부분인데 title은 필수로 받고 desc는 blank=True로 지정해서 없어도 된다는 것으로 볼 수 있다. (이미지도 마찬가지)

- comment_count의 경우는 PositiveIntegerField로 설정했다. IntegerField도 있지만 그러면 음수도 처리할 수 있게 되는데, 댓글의 개수가 음수일리는 없으니까 조금 더 타이트하게 PositiveIntegerField라는 필드로 지정해서 0부터 양수만 처리할 수 있게 지정.

- tag_set은 -> 하나의 글에서 다수의 tag가 들어올 수 있고 / 하나의 Tag도 다수의 글들이 들어올 수 있으니까 ManyToManyField를 지정해주었다.

* * *
## 데이터베이스 설계 시, 꼭 생각해야 할 점
- 설계한 데이터베이스 구조에 따라, 최대한 필드타입을 타이트하게 지정해주는 것이 -> 입력값 오류를 막을 수 있다. 그리고 장고의 이점을 잘 살릴 수 있는 생산성 높은 개발을 할 수 있는 기반이 된다.
- 모델 설계가 Django 개발의 절반이라고 해도 과언이 아니다.
  - blank / null 을 True로 지정하는 것은 최소화하기 (manage.py inspect 명령을 통해 생성된 모델 코드는 초안이다)
  - validators 들을 다양하게 / 타이트하게 지정하기(필요하다면 필요한 만큼의 validators들을 추가로 설정)
  - 프린트엔드에서 유효성 검사는 사용자 편의를 위해서 수행하는 것이지, 백엔드에서의 유효성 검사는 필수이다. 만약, 백엔드에서 하지 않는다면 -> 클라이언트로부터 넘어온 값들을 절대 신뢰해서는 안된다. 누군가가 그러한 로직을 흉내내서 요청을 보낼수도 있는 것이기 때문이다.
  - 직접 유효성 검사 로직을 만들지 말자. 이미 잘 구성된 Features를 가져다 쓰자. 장고의 Form / Model를 통해 지원되며, django-rest-framework의 Serializer를 통해서도 지원된다.

- ORM은 SQL쿼리를 만들어주는 역할일 뿐, 보다 성능높은 애플리케이션을 위해서는 사용하려는 데이터베이스에 대한 깊은 이해가 필요.
