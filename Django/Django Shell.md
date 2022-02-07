## Python의 Interactive shell
- shell은 python 코드를 한 줄 입력받고 그 코드에 대한 실행결과를 그 때 그 때 출력해주는 프로그램을 의미한다.
   - 기본 python shell은 -> terminal에 python만 입력하면 사용가능하다.

- IPython은 기본 python 패키지에는 없고 추가로 설치해야한다.
  - IPython을 설치하는 이유? https://soooprmx.tistory.com/entry/iPython-%EC%84%A4%EC%B9%98%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95
  - IPython 공식 문서 https://ipython.readthedocs.io/en/stable/install/index.html
```terminal
pip install ipython
```

- Jupyter Notebook은 IPython에 대한 웹 버전이다. 서버를 띄우고 웹 브라우저를 통해서 iPython을 쓸 수 있다. 텍스트 이외 이미지 등 UI를 output으로 볼 수 있다. 그래서 데이터 분석이나 머신러닝에 필요하다.
  - 기본 shell에서는 출력된 값들을 텍스트로만 볼 수 있다.


## 기본 python shell / django 프로젝트 설정이 로딩된 shell
- **기본 파이썬 쉘로는 바로 장고 프로젝트의 리소스 (모델, 템플릿 등)를 활용할 수 없다. 왜냐하면 장고 프로젝트 로딩 과정을 거치지 않았기 때문이다. python 입력하고 모델 데이터를 가져오려고 하면 오류가 난다. 즉 현재 python 프로젝트에 대한 로딩이 되어있지 않는 것이다.**
- 그래서, 항상 shell를 띄울 때에는 
```terminal
python manage.py shell
```

- 이렇게 python django 환경이 로딩된 shell를 위의 코드로 띄워야 한다. 만약 여러 shell이 설치가 되어있다면, ipython shell을 먼저 검사하고 -> 그 다음에 bpython shell -> 그 다음에 python shell 이렇게 검사해서 먼저 있는 shell를 우선적으로 띄우게 된다.
  - 만약 아나콘다 환경이라면 conda install ipython / conda install jupyter 이렇게 설치를 진행하면 된다. 
- python manage.py shell 이라고 입력하면 -> 장고 환경이 로딩이 되어있기 때문에 

```terminal
>>> from instagram.models import Post
>>> Post.objects.all()
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>
```

- 이렇게 모델에 접근할 수 있게 된다.

### --command 옵션 활용
```terminal
python -c "print(2**100)"
1267650600228229401496703205376

python manage.py shell -c "print(2**100)"
1267650600228229401496703205376
```

- python shell를 띄울 때 -c 입력하고 문자열을 같이 입력하면, shell로 진입안하고 바로 python 코드를 실행시켜준다. manage.py shell에서도 동일하게 사용 가능하다.


## python shell에서도 번거러우나 가능
- 기본적인 python shell에서도 환경변수를 설정하면 django 환경이 로딩된 shell로 바꿀 수 있다.


- manage.py로 가보면, 
```python
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'askcompany.settings')
```

- 이러한 코드가 있다. django가 시작될 때에 꼭 필요한 환경변수를 의미한다. 그래서 이 DJANGO_SETTINGS_MODULE 환경을 django settings에 알려달라는 것이다. 알려줘야지만, django의 필요한 설정을 읽어드릴 수 있다. 그래서 다시 terminal를 보면,

```terminal
python
>>> import os
>>> os. environ
environ({'APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL': 'true', 'PWD': '/Users/kimsangbaek/Desktop/likelion/env/askcompany', 'USER': 'kimsangbaek', 'COMMAND_MODE': 'unix2003', '__CFBundleIdentifier': 'com.microsoft.VSCode', 'LANG': 'ko_KR.UTF-8', 'LOGNAME': 'kimsangbaek', 'TERM': 'xterm-256color', 'PATH': '/Users/kimsangbaek/Desktop/likelion/env/bin:/Library/Frameworks/Python.framework/Versions/3.9/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Users/kimsangbaek/Desktop/likelion/env/bin:/Library/Frameworks/Python.framework/Versions/3.9/bin', 'SHLVL': '3', 'TERM_PROGRAM_VERSION': '1.64.0', 'SSH_AUTH_SOCK': '/private/tmp/com.apple.launchd.OkGEl82HRG/Listeners', 'TERM_SESSION_ID': '410D5595-89C2-4526-8862-9F97AF16DA38', 'HOME': '/Users/kimsangbaek', 'SHELL': '/bin/zsh', '__CF_USER_TEXT_ENCODING': '0x1F5:0x3:0x33', 'TMPDIR': '/var/folders/dq/1dxvxmwj08g4l_cjw5cp8b_r0000gn/T/', 'TERM_PROGRAM': 'vscode', 'XPC_SERVICE_NAME': '0', 'XPC_FLAGS': '0x0', 'ORIGINAL_XDG_CURRENT_DESKTOP': 'undefined', 'COLORTERM': 'truecolor', 'VSCODE_GIT_IPC_HANDLE': '/var/folders/dq/1dxvxmwj08g4l_cjw5cp8b_r0000gn/T/vscode-git-56b9b3fff9.sock', 'GIT_ASKPASS': '/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/git/dist/askpass.sh', 'VSCODE_GIT_ASKPASS_NODE': '/Applications/Visual Studio Code.app/Contents/MacOS/Electron', 'VSCODE_GIT_ASKPASS_EXTRA_ARGS': '--ms-enable-electron-run-as-node', 'VSCODE_GIT_ASKPASS_MAIN': '/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/git/dist/askpass-main.js', 'OLDPWD': '/Users/kimsangbaek/Desktop/likelion/env', 'VIRTUAL_ENV': '/Users/kimsangbaek/Desktop/likelion/env', 'PS1': '(env) %n@%m %1~ %# ', '_': '/Users/kimsangbaek/Desktop/likelion/env/bin/python'})

>>> os.environ['DJANGO_SETTINGS_MODULE']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Library/Frameworks/Python.framework/Versions/3.9/lib/python3.9/os.py", line 679, in __getitem__
    raise KeyError(key) from None
KeyError: 'DJANGO_SETTINGS_MODULE'

>>> os.environ['DJANGO_SETTINGS_MODULE'] = 'askcompany.settings'

>>> os.environ['DJANGO_SETTINGS_MODULE']
'askcompany.settings'
```

- os. environ은 -> 현재 프로세스에서의 환경변수 목록을 의미한다. 그 환경변수 목록에서 django의 환경변수에 key로 접근하면, 없다고 KeyError가 나온다. 장고 환경이 로딩된 shell이 아니기 때문이다.
- 그래서 os.environ['DJANGO_SETTINGS_MODULE'] = 'askcompany.settings' 이렇게 '프로젝트이름.settings' value를 주고나서 다시 접근을 해보면 value가 나오게 된다.
  - 이러한 과정이 외부에 있는 환경변수를 바꾼 건 아니고, 현재 프로세스 상에서의 환경변수만 수정한 것이다.
  
```terminal
>>> import django
>>> django.setup()

>>> from instagram.models import Post
>>> Post.objects.all()
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>
```

- 위에 이어서, django를 Import하고 setup를 해주면 -> django 환경이 로딩이 된 것이다.
  - 이제 다시 django 모델에 접근해보면 가능해지는 것을 확인할 수 있다.

* * *
- 추가로, Jupyter 환경으로도 설정할 수 있다. 만약, django 모델과 연계해서 분석을 한다거나 크롤링 작업을 해서 모델을 통해 저장을 하는 경우 더 편리하게 사용할 수 있다.
