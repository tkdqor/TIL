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



### 어드민 페이지란
- **어드민 페이지를 개발자만 다루지는 않고 다양한 분야의 사람들이 접근하기 때문에, 관계자들마다 계정을 하나씩 만들어주면서 접근 권한을 따로 줄수도 있다.**
- User 모델을 클릭해서 dev라는 계정은 --> 최상위 사용자 권한을 체크해제 해보자. 그리고 밑에 보면 그룹과 사용자 권한이라는 부분이 나뉜다.

<img width="781" alt="image" src="https://user-images.githubusercontent.com/95380638/162004765-75077c8d-4c2f-4ca3-a8d9-f3928e365d93.png">

- 먼저 사용자 권한 부분은 위와 같이 맨 앞에 App명이 나와있고 / 그 뒤에는 모델명 / 그 다음에는 데이터 추가,수정,삭제 등의 권한을 의미한다. 그래서 이러한 권한을 내가 임의로 지정할 수 있다.

<img width="936" alt="image" src="https://user-images.githubusercontent.com/95380638/162005633-5f828578-1d8f-4f61-91d4-43c4e9bd13aa.png">

- **그래서 이렇게 Post 모델에 대해서 글을 추가하고 읽을수만 있게끔 권한을 설정해줄 수 있는 것이다.**
  - 이 상태에서 어드민 페이지에 dev 계정으로 로그인을 해보면, User랑 Group이랑 Comment 모델은 아예 사라지고 Post 모델만 볼 수 있도록 되어있다. 그리고 post 데이터를 1개씩 클릭해도 안에 내용만 볼 수 있을 뿐이지, 수정이나 삭제가 불가능하게 되어있다. 단, 우리가 설정한 것처럼 post 데이터를 추가할 수는 있다.

- **그런데, 우리가 최초로 생성했던 admin 계정의 경우, 선택된 사용자 권한이 1개도 없는데 모든 걸 다 할 수 있다. 그 이유는, "최상위 사용자 권한" 이라는 부분에 체크가 되어있기 때문에, 슈퍼유저이기 때문에 모든 권한이 들어간 것이다.**

* * *
- **만약, 나중에 관리자가 20명, 30명되서 일일이 권한을 설정하기가 어려워진다면 --> 인증 및 권한에 있는 Group이라는 모델을 활용하면 된다.**
  - 이 모델에 들어가서 "그룹 추가"를 누르면 --> 그룹의 이름과 권한을 설정해줄 수 있다.
  - ex) 이름은 게시글 담당자 / 권한은 게시글 관련 권한만 추가해서 그룹을 생성해줄 수 있다.

<img width="941" alt="image" src="https://user-images.githubusercontent.com/95380638/162007791-a985a7f3-3b69-4a20-b169-e4725aa31d2a.png">

- 그리고나서 다시 User 모델에 들어가서 User 1명을 클릭해보면,

<img width="980" alt="image" src="https://user-images.githubusercontent.com/95380638/162008043-548cf88a-2e07-4c64-9860-74d836976c63.png">

- 이렇게 게시글 담당자 
