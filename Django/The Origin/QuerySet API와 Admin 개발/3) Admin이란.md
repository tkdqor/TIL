## Admin이란
- django가 좋은 이유 중 하나는, Admin이라는 관리자, 모델에 대한 관리자 인터페이스를 제공해준다는 점이다. 다른 곳에서는 관리자 페이지를 별도로 만들어야 한다.

- 우리가 만들어놓은 liongram 위치에서 일단 서버를 가동시키자. 그리고나서 localhost:8000/admin 해당 경로로 들어가면 admin 페이지가 나온다. 여기서 우리가 만든 admin 계정으로 로그인하자.
  - **그런데 지금은 admin 페이지가 영어로 나온다. 이걸 한글로 바꾸려면, settings.py로 가서 내려보면 LANGUAGE_CODE = 'en-us' 이렇게 설정되어 있는 것을 볼 수 있다.** 이걸

```python
LANGUAGE_CODE = 'ko-kr'
```

- **이렇게 수정해주고 새로고침하면 한글로 바뀐다. django가 기본적으로 다국어 처리를 제공해준다고 했는데, 이렇게 한글도 지원이 된다.**
- 이러한 Admin 페이지를 보면, 우리가 로그인/로그아웃 기능을 만들지도 않았는데 제공해주고 비밀번호 변경 및 모델 데이터 추가,변경,삭제가 모두 가능하도록 설정되어있다. 이렇게 Admin 페이지가 작동할 수 
  있는 코드는 어디에 있을까?
  


### Admin 페이지 코드
- 먼저 **settings.py**로 가면 

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'posts',
]
```

- **제일 상단에 admin이라는 앱이 설치된 것을 확인해볼 수 있다.** 그래서 우리한테 admin 페이지를 제공해줄 수 있는 것이다.
- 그리고 config 디렉터리 - urls.py로 가면 --> 기본적으로 admin 페이지에 대한 url pattern이 정의되어 있다.

```python
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
```

- **그래서, 이 admin.site.urls에 command로 클릭해보면,**

```python
    @property
    def urls(self):
        return self.get_urls(), "admin", self.name
```

- **이렇게 urls라는 함수로 정의되어있다.** 해당 함수는 self에서 get 유저를 가져와서 admin의 self.name을 보내게 된다. 그리고 **get_urls() 함수**도 클릭해보면

```python
def get_urls(self):
        # Since this module gets imported in the application's root package,
        # it cannot import models from other applications at the module level,
        # and django.contrib.contenttypes.views imports ContentType.
        from django.contrib.contenttypes import views as contenttype_views
        from django.urls import include, path, re_path

        def wrap(view, cacheable=False):
            def wrapper(*args, **kwargs):
                return self.admin_view(view, cacheable)(*args, **kwargs)

            wrapper.admin_site = self
            return update_wrapper(wrapper, view)
            ...
```

- 이런식으로 정의가 되어있다. 기본적으로 admin_view를 가져오고, 위에는 없지만 url pattern에 로그인, 로그아웃, 패스워드 변경 등이 보인다.


- **그래서, 이런것들을 제공해주는 코드가 이미 django에 있기 때문에 --> 우리가 admin 페이지를 사용할 수 있게 되는 것이다.**


* * *
- **심지어 admin 페이지에는 필터 기능 및 검색 기능도 있다.**

- 일반적으로 우리가 서비스를 관리할 때 쓰는 admin 페이지는 대부분 유사한 인터페이스를 가지고 있다. 테이블 형식의 데이터 출력, 데이터 수정 및 삭제. 
- django admin 페이지는 일부만 검색해도 출력이 된다. User 모델 admin 계정의 성과 이름을 수정해보고 검색하면 나온다.



### User 모델 필드
- User 계정 중, 1개는 권한 부분에 "활성"을 체크해제 하고, 다른 하나는 스태프권한, 최상위 사용자권한을 해제하자.
  - **스태프 권한을 해제하면 어드민 페이지에 로그인이 되지 않는다. 그리고 활성을 해제한 계정은 로그인 자체가 불가능하게 만든다.** 



### 우리가 설정한 models.py 모델 테이블 어드민 페이지에 추가하기
- 프로젝트 루트 디렉터리 내부에 admin.py라는 파일이 있다. 여기에 코드를 입력하면 어드민 페이지에 출력할 수 있다.
