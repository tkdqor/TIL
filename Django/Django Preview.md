## Django 프로젝트 생성
```terminal
pip install "django~=3.0.0"
django-admin startproject 프로젝트명 
python -m django startproject 프로젝트명 
```

- 가상환경이 활성화된 상태에서, 위의 첫번째 코드를 입력하면 djnago의 3점대 중 최신 버전이 설치가 된다.
- 그리고 처음 프로젝트를 시작하기 위해서는, 2번째 혹은 3번째 명령어로 실행한다.




* * * 

### 터미널에서 디렉터리 생성/삭제 및 현재 디렉터리 열기
```terminal
mkdir 생성할 디렉터리 이름
rmdir 삭제할 디렉터리 이름
```

- 이렇게 터미널에 입력하게 되면, 원하는 위치에서 새로운 디렉터리를 생성하고 삭제할 수 있다.

```terminal
open .
```

- 그리고 현재 위치한 상태에서 위와 같은 코드를 입력하면, 현재 위치한 디렉터리를 열어서 새로운 창을 띄워준다.


### 프로젝트 디렉터리 내부
- 처음 프로젝트를 생성하면 생성한 디렉터리 내부에 -> 똑같은 이름을 가진 디렉터리 하나(그 안에도 여러 개의 python 파일들이 있음)와 manage.py가 생성된다. 이러한 파일들은 django가 기본으로 생성해주는 파일들이다.
- 이러한 파일들은, 가상환경 디렉터리 -> lib -> python3.9 -> site-packages -> django -> conf -> project_template에 위치한 내역으로 생성된 것이다.

- 프로젝트 이름으로 생성한 디렉터리 내부 -> 똑같은 이름의 디렉터리는, 함부로 이름을 바꾸면 안된다. manage.py과 settings.py 그리고 wsgi.py가 해당 이름을 참조하고 있기 때문에 폴더명을 바꾸게 되면, 앞의 python 파일의 경로명을 수정해주어야 한다.
  - 반면에, 프로젝트 이름으로 생성한 디렉터리의 이름은 다른 이름으로 변경해도 상관없다.

- **manage.py**는 우리가 개발할 때의 프로그램 진입점이다. 여러 django 명령들을 manage.py를 통해서 수행할 수 있다.
- **settings.py**는 현재 프로젝트의 django 기본 설정(django/conf/global_settings.py)을 덮어쓰고, 우리가 새롭게 설정할 내용들을 해당 파일에 지정할 수 있다. 
- **urls.py**는 최상위 url 설정을 하기 위한 파일이다.
- **던더init던더.py**는 모든 python 패키지에 던더init던더.py를 둔다. 패키지를 import할 때 import 대상이다.
- **wsgi.py**는 실서비스에서의 웹서비스 진입점을 의미.
- **asgi.py**는 Django 3.0 이상부터는 asgi.py가 추가되었는데, 비동기 쪽 지원이 추가된 프로토콜을 의미한다. wsgi.py와 함께 이 2가지는 django의 실서비스에서의 진입점이 된다.
