## Django admin
- 장고 기본에서 지원하는 admin은 몇 가지 설정만으로 데이터베이스 테이블 CRUD (생성, 조회, 수정, 삭제) 기능을 제공해준다. admin의 UI 흐름을 변경할려는 시도는 자칫 개발 난이도를 높일 수 있으나, admin의 한계를 알고, 그 흐름을 따라 세팅을 하신다면 편리하게 사용할 수 있다. admin은 데이터베이스 테이블 데이터 관리툴 정도로 생각하고, 보다 전문적인 관리자 툴이 필요하다면 하나의 페이지를 만들어서 구성하면 된다.


## App별로 관리하는 admin.py
- 새로운 app를 만들고나서 admin 페이지에 관리하기 위해서는, app 내부에 있는 admin.py에서
```python
from django.contrib import admin
from .models import Post

# Register your models here.

admin.site.register(Post)
```
이런식으로 같은 디렉토리 안에 있는 models.py에서 Post 모델 클래스를 import하고, 밑에는 admin site에 Post 모델을 등록해주는 코드를 입력하면 된다.

- 그리고나서 어드민 페이지에 접속하면
<img src="https://user-images.githubusercontent.com/95380638/149297335-0a8fad0a-c263-41d2-8fab-ae7a380b996f.png" width="50%" height="50%">
이렇게 Post 모델이 등록된다.


## django에서 지원하는 기본 앱
- django 폴더 안에 -> contrib 폴더 안에 -> admin 앱을 통해 기능이 제공되는 것. 디폴트 경로는 /admin/ 이다. -> 실제 서비스에서는 다른 경로로 하는 것이 권장된다.

```python
from django.contrib import admin
from django.urls import path, include      # include를 새롭게 추가

urlpatterns = [
    path('admin/', admin.site.urls),
]
```

- 프로젝트 디렉터리 urls.py에서 보면, admin/로 시작하는 주소이면 -> django.contrib.admin 앱에서 처리하겠다는 선언이 되어있는 걸 볼 수 있다. 해당 주소는 다른 것으로 변경가능하다. 주소를 임의의 주소로 변경해도 내부에서 이 주소를 알아서 찾아간다. 이러한 기능을 **URL Reverse**라고 한다. 다른 프레임워크와 다르게 Django는 URL reverse를 적극적으로 활용한다. 

- 또한, django-admin-honeypot이라는 앱을 통해 가짜 admin 페이지를 노출할 수도 있다. 장고로 만들어진 admin 서비스는 별다른 설정을 하지 않으면, /admin/ 이 주소로 접근했을 때 바로 admin 페이지가 뜨게 된다. 이렇게 뜨면 '아 django구나' 라고 알 수 있게 된다.
  - 그래서 honeypot앱을 활용하면 어드민 주소를 변경하고 honeypot앱에 변경된 주소를 매핑을 시킨다. 그렇게 매핑시키면 동일하게 어드민 화면이 뜨는데, honeypot 앱에서는 절대 로그인을 허용하지 않는다. 그리고 로그인을 한 사람의 IP 정보등을 수집해서 DB에 기록하게 된다. 


## Django 모델 클래스 등록을 통해 조회/추가/수정/삭제 웹 UI를 제공
- django admin 페이지를 활용하면 서비스 초기에 데이터베이스(DB) 관리도구로서 사용하기에 제격이다. 그래서 관리도구를 만들 시간을 줄이고, End-User 서비스에 집중해서 개발할 수 있다.
- 또한, django admin은 내부적으로 Django Form를 적극적으로 사용한다.
  - 우리가 admin 페이지에서 모델 하나에서 추가 버튼을 누르면, title과 필드에 맞춰 위젯 형식을 보여준다. 그리고 save를 눌렀을 때 **유효성 검사**를 해준다.(캡처 빨간색 표시) 이러한 것들이 django form에서 해주는 기능들이다. 
  - 이와 유사한 기능이 django-rest-framework의 serializer라고 할 수 있다.
  <img src="https://user-images.githubusercontent.com/95380638/152324412-c488c6fe-c8c5-49ef-8b1c-c6faea3a6aea.png" width="70%" height="70%">


## 모델 클래스를 admin에 등록하는 방법
- 우리가 models.py에서 설정한 클래스로 모델을 만들고 -> 이걸 admin 페이지에서 사용하기 위해서는 생성한 App 디렉터리 -> admin.py에서 등록을 해주어야 한다.

**1) 첫번째 방법은 admin.site.register(모델 클래스 이름) 활용**
   - 모델만 알면, 기본 모델 admin으로 동작하게 된다. (admin앱 내부에 -> ModelAdmin으로 동작)
   - 특정 모델에 대해서 admin에 등록하는 것은 한 번만 된다. 이미 등록된 모델에 대해서는 register를 해지하고나서 새롭게 등록을 할 수 있다.

```python
from django.contrib import admin
from .models import Post        # 같은 같은 디렉터리 위치에 있는 models.py의 Post 클래스를 import


admin.site.register(Post)       # Post 모델 등록
```

- 이렇게 저장하고나서 다시 admin 페이지를 보면 모델이 생성된 것을 확인할 수 있다. 그리고 **add 버튼**을 눌렀을 때 우리가 설정한 필드에 맞춰서 UI가 나오게 된다.
  - 우리가 설정한 모델의 필드에는, **created_at = models.DateTimeField(auto_now_add=True) / updated_at = models.DateTimeField(auto_now=True)**  이렇게 설정했는데 admin 페이지 Form에서는 보이지가 않는다! 
  - 그 이유는, 옵션으로 auto_now_add는 이 레코드가 생성될 때, 즉 데이터베이스에 insert 될 때(모델을 통해 DB에 저장될 때) 시각이 자동으로 입력이 되기 때문이다.(그래서 add버튼 누른 화면에는 없는 것이다) 그리고 auto_now의 경우에는 이 모델을 통해서 DB를 수정할 때의 시각이 자동으로 입력되기 때문이다. 그래서 둘 다 자동으로 입력되는 부분이기 때문에 Form에는 노출이 되지 않는 것이다. 
<img src="https://user-images.githubusercontent.com/95380638/152327896-dd6b4dd7-4860-4b2a-b5a2-1e24e655abc8.png" width="70%" height="70%">


**2) 두번째 방법은 우리가 커스텀으로 어떠한 임의의 이름으로 ModelAdmin을 상속받아서 만들수도 있다.**
```python
from django.contrib import admin
from .models import Post        # 같은 같은 디렉터리 위치에 있는 models.py의 Post 클래스를 import

class PostAdmin(admin.ModelAdmin):
    pass   # 참과 거짓에 따라 실행할 문장 혹은 동작을 정의할 때, 아무런 일도 발생하지 않게 설정하는 것 

admin.site.register(Post, PostAdmin)
```

- 이렇게만 하면, 상속을 받고나서 pass로 아무것도 하지 않았기 때문에 기본적인 Django admin과 동일한 기능이다. 
- 상속받은 클래스 내부에서 -> Post 모델과 우리가 임의로 정한 클래스 이름인 PostAdmin를 설정해주면 된다.


**3) 세번째 방법은 python의 장식자 문법을 사용**
```python
from django.contrib import admin
from .models import Post        # 같은 같은 디렉터리 위치에 있는 models.py의 Post 클래스를 import

@admin.register(Post)           # Wrapping
class PostAdmin(admin.ModelAdmin):
    pass   # 참과 거짓에 따라 실행할 문장 혹은 동작을 정의할 때, 아무런 일도 발생하지 않게 설정하는 것 
```

- python의 장식자 문법은 어떠한 대상이라도 랩을 씌우듯이 wrapping, 즉 감싸는 것이다. 그래서 위와같이 클래스 자체를 장식할 수 있다. 그리고 감싼 대상의 기능을 변경할 수 있다.
- 이렇게 하고나서 저장을 하면, 똑같이 admin 페이지가 나온다.
  - @admin.register(모델 클래스 이름) 이렇게 설정하고 -> ModelAdmin를 상속받는 새로운 클래스를 정의하자.

* * *

## 모델 클래스에서 던더str던더 메소드 구현(models.py에서 진행)
- **admin 페이지 모델 리스트에서 "모델명 object"를 원하는대로 변경하기 위해 사용(DB 데이터 1개에 대한 출력명 변경)**
- 객체를 출력할 때, 객체.던더str던더()의 return 값을 활용 

<img src="https://user-images.githubusercontent.com/95380638/152333418-a2fe71f5-32b6-43de-9342-ce032d56fded.png" width="70%" height="70%">

- 던더str던더 메소드를 구현하지 않고 그냥 admin 페이지에서 데이터를 생성하면, 위와같이 Post라는 모델의 object로 기본적인 표현을 해준다.

- Django 모델뿐만이 아니라, python 클래스에서는 JAVA의 toString과 유사하게 어떤 객체에 대한 문자열이 표현이 필요할 때가 있는데, 그럴 때 python에서는 던더str던더 메소드라는 것을 통해서 우리가 구현할 수 있다. django 모델에 대한 던더str던더 메소드의 기본 구현이, 위에서 보이는 것처럼 **Post object (1)** 이러한 형식으로 되어있다. 

```python
from django.db import models


class Post(models.Model):       # 우리가 원하는 데이터베이스에 저장하고 싶은 내역대로 설계를 해서 사용하면 된다.
    message = models.TextField()    # 기본 default 값이 blank=False이다.
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 

    # Java의 toString
    def __str__(self):
        return f"Custom Post object ({self.id})"
```

- 따라서, 던더str 메소드가 기본적으로 위와 같이 되어있다는 것을 표현해볼 수 있다.
  - 모델 필드에는 기본적으로 id 필드가 있으니까 -> self.id처럼 사용할 수 있다.

- **또 한가지는, 이렇게 모델에서 던더str 메소드를 수정하고나서 바로 admin 페이지를 새로고침하면 반영이 된다는 점을 알 수 있다.**

```python
from django.db import models


class Post(models.Model):       # 우리가 원하는 데이터베이스에 저장하고 싶은 내역대로 설계를 해서 사용하면 된다.
    message = models.TextField()    # 기본 default 값이 blank=False이다.
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 

    # Java의 toString
    def __str__(self):
        # return f"Custom Post object ({self.id})"
        return self.message
```        

- 그리고, 위와같이 모델의 필드로 접근해서 문자열을 표현할 수도 있다.
  - 지금은 목록에 column이 하나밖에 없지만, admin.py에서 ModelAdmin를 상속받아 생성한 클래스를 커스텀해서 우리가 원하는 column들을 추가하거나 검색 UI, 필터링 UI, 커스텀 액션등을 구현해볼 수 있다.
  - 현재는 웹이지만 프론트엔드 코딩을 거의 하지 않고도 장고 admin 단의 python 코드 변경만으로도 수월하게 로직을 적용할 수 있다.

* * *

## admin.py에서 list_display 속성 정의(모델 리스트에 출력할 컬럼 지정이 가능) -> ModelAdmin 클래스에 속성(옵션)을 지정하는 것

```python
from django.contrib import admin
from .models import Post        # 같은 디렉터리 위치에 있는 models.py의 Post 클래스를 import


@admin.register(Post)           # wrapping
class PostAdmin(admin.ModelAdmin):
    list_display = ['pk']
```    

- admin.py에 장식자로 설정된 클래스에서 -> list_display라는 리스트를 만들어주기 / 그리고 안에는 해당 모델에 대한 속성명(column의 이름)을 적어줄 수 있다.
  - **ex) id의 경우, 실제 필드명은 Primary Key가 id인데 id에 대한 Alias, 즉 별칭으로써 pk를 사용할 수 있다. 만약, id가 아닌 다른 message라는 필드가 Primary Key라면 message라는 필드도 pk라는 이름의 Alias로 접근할 수 있다.**
  - 이렇게 설정하면 -> admin 페이지에서 pk가 목록에 나오게 된다.

```python
from django.contrib import admin
from .models import Post        # 같은 디렉터리 위치에 있는 models.py의 Post 클래스를 import


@admin.register(Post)             # wrapping
class PostAdmin(admin.ModelAdmin):
    list_display = ['id', 'message', 'created_at', 'updated_at']
```

- 그래서, 다음과 같이 코드를 입력하면
<img src="https://user-images.githubusercontent.com/95380638/152338790-62e8b485-77cb-4a3f-8899-3629107ea475.png" width="70%" height="70%">

- admin 페이지에 해당 필드명과 데이터들을 바로 보여준다. 그리고 첫번째 column에 링크가 잡혀있게 된다.
  - 만약, 링크를 다른 column에 잡고 싶다면, admin.py에서 list_display_links라는 코드로 특정 필드를 선택하면 된다.

```python
@admin.register(Post)             # wrapping
class PostAdmin(admin.ModelAdmin):
    list_display = ['id', 'message', 'created_at', 'updated_at']
    list_display_links = ['message']
```

- 그리고, list_display에는 models.py에 정의되어있는 function도 사용가능하다.

```python
class Post(models.Model):       # 우리가 원하는 데이터베이스에 저장하고 싶은 내역대로 설계를 해서 사용하면 된다.
    message = models.TextField()    # 기본 default 값이 blank=False이다.
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 

    # Java의 toString
    def __str__(self):
        return self.message

    def message_length(self):
        return len(self.message)
    message_length.short_description = "메세지 글자수"    
```

- 이렇게 models.py에서 함수를 정의하고, 

```python
@admin.register(Post)             # wrapping
class PostAdmin(admin.ModelAdmin):
    list_display = ['id', 'message', 'message_length', 'created_at', 'updated_at']
    list_display_links = ['message']
```

- admin.py에는 다음과 같이 추가해주자. 그러면 admin 페이지에 message 필드의 길이를 나타내준다.

<img src="https://user-images.githubusercontent.com/95380638/152456669-757a6986-99c4-435c-aa46-4cefa63266b1.png" width="70%" height="70%">

- models.py에서 "메세지 글자수" 와 같이 필드명을 수정하는 코드는 인자없는 함수만 가능하다.
  - 또한, python에서는 @property라는 장식자를 통해서 함수 속성을 정의할 수 있다.

- 방금 위에서 진행한 message_length 함수를 -> models.py에서가 아닌 admin.py에서도 구현 가능하다.
  - 만약 models.py에 message_length 함수를 정의하는 코드가 없는 상태에서

```python
@admin.register(Post)             # wrapping
class PostAdmin(admin.ModelAdmin):
    list_display = ['id', 'message', 'message_length', 'created_at', 'updated_at']
    list_display_links = ['message']

    # 이 함수는 post 모델의 객체가 넘어오는 것이다. 이건 admin이 알아서 호출해주는 것이다.
    def message_length(self, post):
        return len(post.message)            # 또는 return f"{len(post.message)} 글자"  이렇게도 사용가능하다.
```

- admin.py에서 message_length 함수를 정의해주면 그대로 실행이 된다.

* * *
## search_fields 속성 정의
- admin.py에서 클래스 내부에 search_fields 라는 속성을 추가하면, admin 내 검색 UI를 통해 DB쪽으로 where 쿼리를 날려서 대상 필드를 리스트 할 수 있다.

- 먼저 장고 shell를 띄워보자. 프로젝트 디렉터리 안에있는 경로에서 터미널에 
```terminal
python manage.py shell
```

- 이렇게 입력. 그리고,

```terminal
>>> from instagram.models import Post
>>> Post.objects.all()

<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>
```

- **QuerySet은 데이터베이스로부터 전달 받은 객체의 목록이다. Django ORM에서 발생한 자료형으로 리스트와 구조는 같으나, python에서 읽고 쓰기 위해서는 자료형 변환을 해줘야 한다.** 
- Post.objects.all() 이 코드로 전체 Post 모델에 대한 모든 데이터를 데이터베이스로부터 가져올 수 있다. 

```terminal
>>> Post.objects.all().filter(message__icontains='첫번째')

<QuerySet [<Post: 첫번째 메세지>]>

>>> qs = Post.objects.all().filter(message__icontains='첫번째')
>>> print(qs.query)

SELECT "instagram_post"."id", "instagram_post"."message", "instagram_post"."created_at", "instagram_post"."updated_at" FROM "instagram_post" WHERE "instagram_post"."message" LIKE %첫번째% ESCAPE '\'
```

- 이 때, 필터를 통해서 우리가 message라는 필드에 icontains라는 ignore 케이스가 포함된 contain를 주게 되면, 즉 대소문자를 구별하지 않는 쿼리를 날리겠다는 것이다. SQL에서는 ilike절이 추가되어서 전달이 된다.
  - 그렇게 코드를 입력하면 다음과 같이 첫번째 메시지가 나온다.
  - 그리고나서 qs라는 변수에 해당 내용을 저장해주자. 또한, 그 다음에 print(qs.query)라고 입력해보면 -> 실제 DB에 전달된 쿼리를 확인해볼 수 있다. 해당 쿼리문을 보면, where 조건에 message에 대해서 조건을 LIKE로 걸어준 게 보인다.

- 이러한 일련의 과정을, 우리가 admin.py에서 search_fields를 통해서 구현해볼 수 있다.

```python
@admin.register(Post)             # wrapping
class PostAdmin(admin.ModelAdmin):
    list_display = ['id', 'message', 'message_length', 'created_at', 'updated_at']
    list_display_links = ['message']
    search_fields = ['message']

    # 이 함수는 post 모델의 객체가 넘어오는 것이다. 이건 admin이 알아서 호출해주는 것이다.
    def message_length(self, post):
        return f"{len(post.message)} 글자"
```

- admin.py에서 이렇게 입력하고 새로고침하면 -> admin 페이지에 검색할 수 있는 bar가 새롭게 생성이 된다. 그래서 여기에 '첫번' 이렇게만 입력해도, message 필드에 있는 '첫번째 메세지'의 데이터가 조회된다.
- 이 때, url를 보면 localhost:8000/admin/instagram/post/?q=첫번   -> 이렇게 변한다. q라는 이름의 querystring에 '첫번'이 전달되는 것이다. 즉, 쿼리가 DB를 호출하는 명령어이니까 DB에 '첫번'을 검색해달라고 하는 것이다.
  - 사실 내부적으로는 위의 shell을 이용한 로직을 타서 -> 데이터베이스에 아까 조회했던 쿼리문을 날려서 그 결과를 admin 페이지에 보여주는 것이라고 할 수 있다. 이렇게 admin.py에서 편리하게 사용가능하다는 것이다.

* * *
## list_filter 속성 정의
- list_filter 속성은 지정한 필드값으로 필터링 옵션을 제공하는 속성이다.

```python
@admin.register(Post)             # wrapping
class PostAdmin(admin.ModelAdmin):
    list_display = ['id', 'message', 'message_length', 'created_at', 'updated_at']
    list_display_links = ['message']
    list_filter = ['created_at']
    search_fields = ['message']
```

- admin.py에서 다음과 같이 정의한 클래스 내부에 list_filter라는 속성에 특정 필드값, 예를 들어 created_at를 지정하자. 그 다음 admin 페이지를 보면 

<img src="https://user-images.githubusercontent.com/95380638/152460815-c7651cf5-ef6a-42b3-99f4-2b969d36877d.png" width="70%" height="70%">

- 다음과 같이 오른쪽 측면에 필터가 추가됨을 확인할 수 있다.


- **또한, models.py에서 Post 모델에 is_public이라는 필드를 추가해보자. -> 해당 글을 공개할지말지 결정하는 필드이다.**
```python
class Post(models.Model):       
    message = models.TextField()  
    is_public = models.BooleanField(default=False, verbose_name='공개여부')
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
```

- 이렇게 is_public이라는 필드를 추가하고, 기본값은 False이고 필드명을 '공개여부'로 수정했다. **그리고 이렇게 Django의 모델을 수정했다면 -> makemigrations / migrate 을 진행해야 한다.**

```terminal
python manage.py makemigrations instagram
python manage.py migrate instagram
python manage.py showmigrations instagram

instagram
 [X] 0001_initial
 [X] 0002_post_is_public
 
python manage.py showmigrations

admin
 [X] 0001_initial
 [X] 0002_logentry_remove_auto_add
 [X] 0003_logentry_add_action_flag_choices
auth
 [X] 0001_initial
 [X] 0002_alter_permission_name_max_length
 [X] 0003_alter_user_email_max_length
 [X] 0004_alter_user_username_opts
 [X] 0005_alter_user_last_login_null
 [X] 0006_require_contenttypes_0002
 [X] 0007_alter_validators_add_error_messages
 [X] 0008_alter_user_username_max_length
 [X] 0009_alter_user_last_name_max_length
 [X] 0010_alter_group_name_max_length
 [X] 0011_update_proxy_permissions
 [X] 0012_alter_user_first_name_max_length
blog1
 [X] 0001_initial
contenttypes
 [X] 0001_initial
 [X] 0002_remove_content_type_name
instagram
 [X] 0001_initial
 [X] 0002_post_is_public
sessions
 [X] 0001_initial
```

- makemigrations를 하게 되면, migrations 디렉터리에 0002 생성된다. 모델에 변경을 가하겠다는 내용이다. 그래서 해당 명령어로 migration 파일을 만들었다.
  - 그리고, 실제 DB에 적용하기 위해서 migrate 명령어를 입력해주자.

- 또한, migrate까지 하고나서 migration 적용 내역을 보려면 -> python manage.py showmigrations instagram 라는 코드를 입력하면, 위의 예시처럼 migrations 파일의 적용 내역을 볼 수 있다.
  - 추가로, python manage.py showmigrations -> 이렇게 앱 이름을 삭제하고 입력해보면, 전체 App 별로 migrations 파일 적용 내역을 확인할 수 있다.


- Post 모델에 is_public 필드를 추가하고, admin.py에서 해당 필드를 admin페이지에 노출시켜보자.
```python
@admin.register(Post)             # wrapping
class PostAdmin(admin.ModelAdmin):
    list_display = ['id', 'message', 'message_length', 'is_public', 'created_at', 'updated_at']
    list_display_links = ['message']
    list_filter = ['created_at', 'is_public']
    search_fields = ['message']
```

- 이렇게 list_display와 list_filter에 해당 필드를 추가하고 새로고침해보면, 

<img src="https://user-images.githubusercontent.com/95380638/152463152-d23fb890-775e-458e-9466-9cc63e51c77d.png" width="70%" height="70%">

- 이렇게 admin 페이지에 Post 모델에 공개여부라는 필드가 생성된 것을 확인할 수 있다. 또한, 우측에는 공개여부를 기준으로 필터링을 할 수 있다.
  - 그래서, 메세지 하나를 클릭하고 공개여부에 체크하고 -> 필터에서 체크된 것만 / 아닌것만 나눠서 볼 수 있다.
  - 참고로, admin 화면 우측 필터는 여러개가 있다면 and 조건을 충족할 때 보여준다.


<img src="https://user-images.githubusercontent.com/95380638/152463612-fb490c61-8684-4e6e-94af-63aace5d4cd9.png" width="70%" height="70%">
- 참고로, 이와 같이 admin 페이지 위에 뜨는 메시지는, django의 messages framework를 통해서 보여지게 되는 것이다.


* * *
- 이외에도, 다양한 admin 기능들이 django에서 제공하고 있다. 자세한 내용은 https://docs.djangoproject.com/en/4.0/ref/contrib/admin/ 여기 django 공식 문서를 참고해보자.
  - 그러면 python 코드만으로도 수월하게 admin 페이지를 관리할 수 있다.
