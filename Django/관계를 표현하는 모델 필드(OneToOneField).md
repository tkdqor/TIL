## OneToOneField
- 1대1 관계에서 어느 쪽이라도 지정 가능
- 공식문서 https://docs.djangoproject.com/en/4.0/ref/models/fields/#onetoonefield
  - 예외적으로 User : Profile 이렇게 2개의 모델이 있을 때, User 모델은 django의 auth앱의 User 모델을 우리가 변경할 수 없기 때문에 Profile에 설정해야 함.

- ForeignKey(unique=True)와 유사하지만, reverse 차이
  - 외래키는 1:N인데 ForeignKey에 unique=True를 지정하면 OneToOneField와 유사하게 동작하지만, reverse name에서 차이가 나게 된다.
    - User:Profile를 FK로 지정한다면 -> profile.user_set.first().       -> Profile에서 User 객체 접근할 때 ??? / first()로 조회하는 이유는 unique=True라서 하나밖에 없기 때문.
    - User:Profile를 O2O로 지정한다면 -> profile.user / 이렇게 모델명이 그대로 사용된다. 만약, Profile에 User가 지정되어 있지 않다면 DoesnotExist라는 예외가 발생하게 된다.


- **OneToOneField(to, on_delete)**
  - O2O필드 역시 외래키와 마찬가지로 to와 on_delete 이렇게 2개의 인자를 지정해줘야 한다.

```python
# django/contrib/auth/models.py
# 우리가 손댈 수 없는 User 모델 부분

class User(AbstractBaseUser):
    ...
    
    
# accounts/models.py
# accounts라는 앱을 만들고 그 내부에 Profile이라는 모델를 정의했다고 가정

class Profile(models.Model):
    author = models.OneToOneField(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
```    
    
- 이렇게 Profile 모델에는 User모델과 O2O 관계를 설정했기 때문에, 하나의 유저는 하나의 프로필을 가지게 된다.    

* * *

### O2O 설정 예시
```terminal
python manage.py startapp accounts
```

```python
INSTALLED_APPS = [
    # django apps
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # locals apps
    'blog1',
    'instagram',
    'accounts',
    # third apps
    'django_extensions',
    'debug_toolbar',
]
```
- accounts 라는 App를 만들고, settings.py에 추가해서 명시. 그리고 accounts 디렉터리 내부에 urls.py를 새롭게 생성.

```python
urlpatterns = [
    
]
```

- urls.py를 이렇게 설정한 후, 프로젝트 디렉터리 내부 urls.py에 가서
```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog1/', include('blog1.urls')),   # 새롭게 url를 만들어주고 해당 url은 blog1 앱의 urls.py로 보내주기
    path('instagram/', include('instagram.urls')),  # 새롭게 url를 만들어주고 해당 url은 instagram 앱의 urls.py로 보내주기
    path('accounts/', include('accounts.urls')),
]
```

- accounts/ url로 접속할 때, accounts 앱의 urls.py로 연결시켜주자.

* * *

### accounts App model 설정
- 이제 accounts 디렉터리 내부 models.py에 설정하기.
```python
from django.conf import settings
from tkinter import CASCADE
from django.db import models


# Create your models here.

class Profile(models.Model):
    user = models.OneToOneField(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    address = models.CharField(max_length=100)
    zipcode = models.CharField(max_length=6)
```    

- settings.AUTH_USER_MODEL는 -> default가 'auth.user'로 설정되어 있기 때문에 이렇게 입력.
- User:Profile를 1:1 관계로 설정.
- zipcode는 우편번호를 의미. 문자도 포함될 수 있어서 CharField를 선택.

```terminal
python manage.py makemigrations accounts
python manage.py migrate accounts
```

- migrations / migrate까지 완료하면, 이제 accounts_Profile이라는 이름의 모델(테이블)이 데이터베이스에 생성된 것이다.


### accounts App admin.py 설정
- accounts App 내부의 admin.py에서 설정해주기. 제일 먼저 Profile 모델을 import하자.
```python
from django.contrib import admin
from .models import Profile

# Register your models here.

@admin.register(Profile)
class ProfileAdmin(admin.ModelAdmin):
    pass
```    

- 이렇게 입력해주고 admin 페이지를 새로고침해서 확인해보기. 그러면 Accounts 앱의 Profile이라는 모델을 확인할 수 있다. 
- 이제 Profile 모델에 들어가서 Add 버튼을 누르고 User 부분에 현재 User 모델에 존재하는 username를 선택할 수 있다. (물론 pk값 기준이기 때문에 선택한 username의 모델 객체를 설정한다고 보면 된다)
  - 그렇게 해서 save를 눌러주면 Profile 모델의 객체가 1개 생성된다.

- **그리고 다시 Profile 모델에서 add를 누르고, user를 똑같이 설정한 다음 save를 누르게 되면 -> 이미 존재하는 user라고 생성되지 않는다.**
  - **즉, User와 Profile이 1:1 관계(OneToOneField)로 설정되어있기 때문에 동일한 user에 새로운 profile이 생성될 수는 없다.**
  - 만약, 1:N관계(ForeignKey)라면 생성이 되었을 것이다.

- 이렇게, relation을 타이트하게 지정해주면 우리가 원하는 애플리케이션 구조에 맞게 유효성 검사를 django가 잘 수행을 해줄 수 있다.

* * *

### O2O에서의 related_name
- reverse 접근 시의 속성명 : default로 모델명소문자

```python
# accounts/models.py
class Profile(models.Model):
    user = models.OneToOneField(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    address = models.CharField(max_length=100)
    zipcode = models.CharField(max_length=6)
```

- 위와 같이 Profile이라는 모델이 정의되었고 User 모델과 O2O 관계일 때,
```terminal
In [7]: profile = Profile.objects.first()

In [10]: profile.user
Out[10]: <User: askcompany>
```

- 이렇게 Profile 모델에서 User 모델로 접근이 가능하고 / 반대로 user.profile 이렇게 User 모델에서 Profile 모델로 접근이 가능하다.


### django 연동 shell로 확인해보기
```terminal
python manage.py shell

In [1]: from accounts.models import Profile

In [2]: Profile.objects.all()
Out[2]: <QuerySet [<Profile: Profile object (1)>]>

In [3]: profile = Profile.objects.first()

In [4]: profile
Out[4]: <Profile: Profile object (1)>

In [5]: profile.user
Out[5]: <User: sangbaek>
```

- 먼저 위의 과정을 거쳐 User:Profile 1:1 관계에서 Profile 모델에서 User 모델로 접근이 가능함을 확인할 수 있다.


```terminal
# from django.contrib.auth.models import User  -> 이렇게 import 할 수 있지만 권장하지 않음

In [7]: from django.contrib.auth import get_user_model

In [8]: User = get_user_model()

In [9]: User.objects.all()
Out[9]: <QuerySet [<User: sangbaek>]>

In [10]: User.objects.first()
Out[10]: <User: sangbaek>

In [11]: user = User.objects.first()

In [12]: user.profile
Out[12]: <Profile: Profile object (1)>
```

- from django.contrib.auth import get_user_model 이렇게 import를 하게 되면, 현재 활성화된 User 모델을 얻을 수 있는 함수를 import 한 것이다. 꼭 이렇게 사용하길 추천된다.
- user.profile를 하게 되면 -> 1개의 user 객체에 해당하는 pk값을 기준으로, Profile 모델에서 그 pk값에 해당하는 profile 객체를 가져와 주는 것이다. 
- 이렇게 reverse이지만 모델이름으로 접근이 가능하다.

```terminal
In [14]: profile.delete()
Out[14]: (1, {'accounts.Profile': 1})

In [15]: user.profile
---------------------------------------------------------------------------
RelatedObjectDoesNotExist                 Traceback (most recent call last)

```

- profile.delete() 이렇게 입력하면 실제로 DB의 데이터가 지워진다. admin 페이지에서도 지워진 걸 확인할 수 있다.
  - 지운 상태에서 user로 접근해보면 관련된 객체가 없다는 오류가 뜨게 된다.

- user.profile 이라는 코드는 = Profile.objects.get(user=user) 와 같다.


### Profile 모델
- User가 만들어지면 django에 시그널이라는 것이 있다. 일종의 이벤트 핸들러의 개념이다. 
- 그래서 User 모델에서 Post.save()가 이루어지고 나서 호출되는 함수들을 지정할 수 있는데, 거기에서 Profile이 자동생성 되도록 그렇게 로직으로 User와 Profile이 항상 존재함을 구현할 수 있다.

