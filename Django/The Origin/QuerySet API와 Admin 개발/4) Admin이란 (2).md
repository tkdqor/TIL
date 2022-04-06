## Admin이란 (2)


### liongram 디렉터리 내부 models.py에 우리가 설정한 Post와 Comment 모델을 admin 페이지에 추가해보기
- 루트 디렉터리 내부에 있는 admin.py 열기

```python
from django.contrib import admin
from .models import Post, Comment

# Register your models here.

# Post 모델 등록
admin.site.register(Post)

# Comment 모델 등록
admin.site.register(Comment)
```


- **models.py가 admin.py와 마찬가지로 posts 라는 앱 같은 경로에 있기 때문에 --> from .models라고 설정**
- 위와 같이 설정하고 서버를 재실행 후 admin 페이지를 확인해보면 --> 좌측에 2개의 모델이 추가가 되었다.
  - 여기서 각 모델에 대한 데이터를 추가하고 수정 및 삭제가 가능하다.

- **우리가 데이터베이스는 결국 같은 것을 사용하고 있기 때문에, 우리가 django shell을 쓰나 관리자 페이지를 쓰나 데이터베이스 Tool를 사용하나 다 동일한 데이터이기 때문에 동일하게 데이터를 관리할 수 있다. 그래서 기본적으로 데이터에 대한 CRUD가 가능하다.**


### 모델 필드 중, ForeignKey의 경우 -> admin 페이지에서 콤보박스로 표현
- Post 모델의 writer 필드는 ForeignKey로 설정했기 때문에, admin 페이지에서 보면 writer가 콤보박스로 표현되어 있다. 그래서 지금 존재하는 사용자들을 나열해준다. 
- 댓글도 Comment 모델에 들어가서 추가해줄 수 있다. 생성 시, 어떤 post 글인지 어떤 writer인지 선택해주면 된다. 


### Comment 모델 추가 수정
- **created_at 필드에 auto_now_add=True 속성 추가해주기**
- **추가하고 나서 어드민 페이지에서 댓글을 생성하게 되면 --> 작성일 자체를 우리가 입력할 수 없게 사라진다. 자동으로 생성되기 때문이다.**
