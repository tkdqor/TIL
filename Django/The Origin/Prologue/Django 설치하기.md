## Django 설치하기
- django를 설치하기 전에, 새로운 폴더를 생성해주기

```terminal
mkdir first-django
```

- 그리고 vscode에서 해당 경로로 이동해주자.


### pip
- 터미널에서 
```terminal
pip3 list

Package           Version
----------------- --------
astroid           2.8.3
isort             5.9.3
lazy-object-proxy 1.6.0
mccabe            0.6.1
pip               20.2.3
platformdirs      2.4.0
pylint            2.11.1
setuptools        49.2.1
toml              0.10.2
typing-extensions 3.10.0.2
wrapt             1.13.2
```

- 이렇게 맥북이면 pip3로 해서 입력하게 되면, 위와 같이 설치된 패키지와 버전이 나와있다. 
- Package라는 것은 --> python 코드로 변수, 상수, 함수, 클래스들이 뭉쳐있는 python 코드 집합체라고 보면 된다.
  - **그 중에서도 pip는 python package 관리자를 의미한다.** 
  - django도 python package로 취급한다. 그리고 외부에서 가져오는 package는 전부 pip로 설치를 해야 한다.


### 가상환경 만들기
- 우리가 만든 first-django라는 디렉터리 위치에서 가상환경을 만들자. 해당 디렉터리 위치에서

```terminal
python3 -m venv venv
```
<img width="203" alt="image" src="https://user-images.githubusercontent.com/95380638/160289726-bb88973a-f0b9-44a6-9ee9-2220e5c10370.png">

- 이렇게 venv라는 이름의 가상환경을 생성할 수 있다.

- 그리고 이제 가상환경에 접속해보자. first-django에 위치한 상태에서 

```terminal
source venv/bin/activate
```

- 맥은 다음과 같이 입력하면 가상환경이 활성화된다. 윈도우는 .\venv\Scripts\activate 이렇게 입력하면 된다.


### 가상환경에서 django 설치하기
- 가상환경이 활성화된 상태에서 first-django 폴더안에서
```terminal
pip install django 
```

- 이렇게 입력하면 우리가 생성한 가상환경 안에 django 패키지가 설치된 것이다. django를 설치한다고 해서 딱 django만 설치되는 것은 아니다. django에 의존성을 가지고 있는 패키지가 같이 설치된다. 


### django 프로젝트 생성하기
- 가상환경 활성화 되어있고, first-django라는 디렉터리로 들어가 있는 상태에서

```terminal
django-admin startproject config .
```

- 이렇게 입력하면 프로젝트가 생성된다. **원래 startproject 다음에 원하는 프로젝트 이름을 입력하는데, config . 을 입력한 이유는 --> django 프로젝트를 편하게 관리하기 위해서, 유지보수를 하기 위한 실무적 방법이라고 생각하자.**
  - 그래서 config라는 이름의 프로젝트가 생성이 되었다. 

- **즉, 어떤 한 폴더안에 venv라는 가상환경이 있고 -> 그 가상환경 내부가 아닌 같은 위치에 프로젝트를 생성한다. 다시말해 어떤 디렉터리 안에 venv 디렉터리와 프로젝트 디렉터리가 있는 것이다.**

- 그리고 django 프로젝트가 제대로 생성이 되었는지 확인하기 위해서, django를 실행시켜 봐야한다.
  - django를 실행시킬 때는 항상 manage.py라는 파일을 통해서 명령어를 실행시킨다. 지금 내가 있는 경로에 manage.py가 있는지 확인을 해야한다.
  - 지금은 first-django 위치에서

```terminal
python manage.py runserver
```

- 라고 입력하게 되면 django가 실행이 된다. 즉, server를 활성화시킨 것이다. 그리고 브라우저에서 http://127.0.0.1:8000/ 해당 경로로 들어가면 로켓 모양의 페이지가 뜨는 것을 확인할 수 있다.
