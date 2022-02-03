# App별로 관리하는 admin.py
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

1) 첫번째 방법은 admin.site.register(모델 클래스 이름) 활용
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

