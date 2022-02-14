## 테이블 데이터베이스 데이터 채우기
- 원칙은 새로운 데이터를 생성하기 위해 python 코드를 기반으로 모델을 다뤄야 하지만, 먼저 쉬운 방법으로 django의 Admin 기능을 활용해보자.


### Admin 페이지
- 관리자가 서비스를 운영하기 위한 목적으로 내부적으로 사용하는 페이지이다. 먼저 admin를 사용하기 위해서는 관리자 계정이 필요하다.
```python
python manage.py createsuperuser
```

- 이렇게 입력하면 관리자 계정 만들기를 할 수 있다. 계정의 아이디와 비밀번호를 설정한 후 server를 가동시키자. 그리고 localhost:8000/admin으로 들어가서 로그인을 하면 된다.
  - 이렇게 해당 페이지에 접속할 수 있는 이유는, 프로젝트 디렉터리 내부의 urls.py에서 **path('admin/', admin.site.urls),** 다음과 같이 url이 설정되어 있기 때문이다.

- 기본적으로 django admin 페이지는 우리가 관리하고자 하는 모델이 있다면 -> 우리가 이 모델을 직접 등록해줘야 한다.
  - posts 앱 내부에 admin.py을 열고 

```python
from django.contrib import admin
from .models import Post

# Register your models here.

admin.site.register(Post)
```

- 우리가 등록할 Post 모델을 먼저 import 해야 한다. 그리고 admin 모듈을 이용해 Post 모델을 등록하자. 이제 admin 페이지를 새로고침 하면 Post 모델 내용이 추가된다.
  - 여기서 Add를 누르면, Post 모델의 새로운 데이터를 생성 및 수정, 삭제가 가능하다.
    - **Post 모델을 models.py에서 설정할 때, DateTimeField의 auto_now_add=True 속성을 추가했었는데 -> 이 경우에는 admin 페이지에서 Add를 누르면 Created_at에 대한 항목이 아예 없다. 자동으로 현재 시간이 들어가게 된다.**


- admin 페이지에서 4개의 게시물을 추가하고 목록을 보면, Post의 내용물을 출력할 때 우리가 models.py에서 던더str 메소드로 설정했던 형식으로 출력된다. 
  - 기본적으로는 모델의 객체 id가 출력된다.


### django의 default Timezone
- django에서 사용하는 default Timezone, 시간대는 세계 표준인 UTC 협정 세계시를 사용한다. 영국 런던을 기준으로 0시라고 했을 때, 한국은 +9시간을 해줘야 우리에게 익숙한 시간이 된다.
  - 이걸 변경하고자 한다면, 프로젝트 패키지 내부의 settings.py에서 TIME_ZONE = 'UTC' 이렇게 되어있는 것을 볼 수 있다. 이 설정을 TIME_ZONE = 'Asia/Seoul' 우리 시간으로 변경할 수 있다.
  - LANGUAGE_CODE = 'en-us' 도 'ko-kr'이라고 바꿔주면 기본 언어가 한국어로 바뀐다.


## 데이터베이스 데이터 출력하기
- posts 앱 패키지 내부에 views.py에서 데이터베이스에 저장되어있는 데이터를 불러와서 template에 전달해보자.

```python
from django.http import HttpResponse
from django.shortcuts import render
from .models import Post

def index(request):
    posts = Post.objects.all()
    
    context = {
        'posts': posts,
    }
    
    return render(request, 'posts/index.html', context)    
```


- 앱 내부 views.py에서 먼저 Post 모델을 import.
- 그리고 index 함수 내부에 posts = Post.objects.all() 이러한 코드를 입력해서 -> Post 테이블에 저장되어있는 모든 데이터를 불러온다. 모든 데이터들을 posts라는 이름의 변수에 저장한다.
- 또한, context 딕셔너리에도 value 값으로 위에서 설정한 posts를 대입해준다.
  - 이 상태로 index.html에 데이터를 넘겨주게 되면 posts라는 변수를 활용해서 데이터베이스의 데이터를 보여줄 수 있다.

```html
 {% if posts %}
        <ul>
            {% for post in posts %}
                <li>Author: {{ post.author }} | body: {{ post.body }}</li>
            {% endfor %}
        </ul>        
    {% else %}
            <p>게시글이 없습니다!</p>
    {% endif %}
```

- posts에 담겨있는 여러 게시물 정보 중 하나인 post를 뽑아서 Post 모델 객체 1개의 author 필드와 body 필드를 -> post.author / post.body 이렇게 사용할 수 있다.
    
    
