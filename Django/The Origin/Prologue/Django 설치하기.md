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
