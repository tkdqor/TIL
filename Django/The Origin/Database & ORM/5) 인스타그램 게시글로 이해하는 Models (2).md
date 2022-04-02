## 인스타그램 게시글로 이해하는 Models (2)
- first-django와 같은 위치에 liongram이라는 디렉터리 하나 만들기
- 그리고 liongram에 들어가있는 상태에서 python, 가상환경, django를 다시 설치해주자.

```terminal
python3 -V   
python3 -m venv venv
```

- **이렇게 python3 버전 확인 후, liongram 안에서 가상환경을 먼저 만들어준다.**
- 그리고나서 가상환경을 활성화하자.

```terminal
source venv/bin/activate
```

- 이렇게 가상환경을 활성화하면, 가장 먼저 pip 자체를 업그레이드 시켜줘야한다.
```terminal
pip install --upgrade pip
pip list
```
- 업그레이드 이후에는 pip list로 버전을 확인해주자. 이렇게 가상환경이 활성화되어있는 상태에서 이제 django를 설치해주기.

```terminal
pip install django
```

- 여기까지 기본적인 구성을 설치했다.

* * *
- 그 다음에는 liongram안에 django 프로젝트를 새롭게 만들것이다.

```terminal
django-admin startproject config .
```

- **이렇게 config로 설정해서 프로젝트를 만들면 --> 우리가 생성했던 liongram이라는 디렉터리가 django 루트 디렉터리가 된 것이다.**

- **이제 게시글을 만들기 위해 posts라는 이름의 App를 만들어보자.**

```terminal
django-admin startapp posts
```

- 이렇게 App를 추가했으면 무조건 settings.py에 추가를 해줘야 한다.


### posts App의 models.py 설정
- models.py로 가서 Post라는 모델 클래스를 설정해준다.

```python
from django.db import models

# Create your models here.

class Post(models.Model):
    image = models.ImageField(verbose_name='이미지')
    content = models.TextField('내용')
    created_at = models.DateTimeField('작성일')
    view_count = models.IntegerField('조회수')
```

- 일단은 이렇게 간단하게 Post 모델에 들어갈 필드를 설정해봤다.
  - **여기에 추가로 ImageField에 verbose_name이라는 속성을 추가해보자. 관리자 페이지에서 노출되는 해당 필드에 대한 이름을 지정해주는 것이다.** 그래서 여기서는 모든 필드에 해당 속성을 설정해서 한글로 바꿔주었다.

- 이렇게 모델을 구성한 다음, django에서는 이미지를 다룰 때 꼭 Pillow라는 패키지를 꼭 다운로드 해야 한다.

```terminal
python -m pip install Pillow
```

- 그리고나서 이제 우리가 입력한 모델 설정을 실제 데이터베이스에 반영할 수 있도록 migrations 명령어를 입력해야 한다.
```terminal
python manage.py makemigrations 
```


- **migrations는 우리가 작성한 모델 코드들을 실제 데이터베이스에 생성하기 전에 명세한다라고 이해해주면 된다.** 그래서 migrations라는 디렉터리가 알아서 생성이 되고 여기에 첫번째 파일을 들어가보면 어떤 이름의 모델 테이블을 만들것인지, 그 안에 필드는 어떤 것인지 나와있다.
  - **id필드는 자동으로 생성되며 하나씩 숫자가 커지는 BigAutoField로 설정되어있다. 그리고 primary_key=True로 되어있어서 기본키가 True로 되어있으면 고유식별자 필드가 된다.**

- 마지막으로 
```terminal
python manage.py migrate
```

- **이렇게 입력을 하면 post App의 모델 말고도 다른 App이나 패키지의 모델들도 같이 생성이 된다.** 
  - **django에서는 기본적으로 인증 시스템도 있고 User 모델도 만들어주고 하기 때문에 많은 모델 테이블들이 생겼다라고 보면 된다.**
  - 이 때, 꼭 모델 테이블이 생기는 것 뿐만 아니라 alter로 수정이 되는 모델들도 있다. 
  - **이러한 migrate는 migration파일을 기준으로 진행이 된다.**


### django Admin 유저 생성
```terminal
python manage.py createsuperuser
```

- 이렇게 입력해서 아이디는 admin으로 하고 관리자 계정을 생성할 수 있다.

- **그 다음에 VSCode 화면 왼쪽에 5번째 버튼인 확장 프로그램을 선택한 후, sqlite라고 입력하면 sqlite라는 프로그램이 있다.** 그래서 이 프로그램을 설치해준다.
  - **그리고나서 루트 디렉터리에 있는 db.splite3파일을 우클릭해서 --> Open Database를 클릭해주면 밑에 SQLITE EXPLORER라고 뜨게 된다.** 이걸 클릭해서 열어보면 다양한 모델 테이블들이 있는 것을 확인할 수 있다. 여기서 우리가 방금 추가한 **posts_post**, 즉 posts앱의 post 모델 화살표를 클릭해보면 SQLite 화면이 뜬다. 아직 데이터가 없기 때문에 비어있다.
  - django 내부의 User모델은 --> **auth_user**를 클릭해봐야 한다. 그러면 방금 우리가 만든 사용자 계정의 데이터가 보인다.

