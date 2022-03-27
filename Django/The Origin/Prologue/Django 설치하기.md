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
