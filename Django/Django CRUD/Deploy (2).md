## Deploy (2)
- Ubuntu에서 nginx를 설치하게 되면 자동으로 nginx를 시작까지 해준다. 우리의 EC2 인스턴스의 IP 주소나 DNS 주소로 접속하게 되면 바로 확인해볼 수 있다. 
  - 퍼블릭 IPv4 주소를 복사해서 -> 브라우저에 접속해보면 "Welcome to nginx!" 라는 페이지를 확인할 수 있다.


### nginx(WEB Server)
- **nginx는 web server의 한 종류이다. web server에 대표적인 프로그램으로 apache, nginx가 있다.** 
- 우리에게 익숙한 구글 드라이브나 네이버의 엔드라이브, 드롭박스 이런 서비스처럼 우리가 HTML 파일, CSS 파일, Javascript 파일 그리고 이미지와 같은 static 파일들을 server에 저장해두면 -> 사용자는
  해당 경로에 있는 파일을 요청하고 그냥 보기만 할 수 있었다. 이 과정에서 어떤 연산도 처리할 수 없었다. 폼을 통해서 데이터를 전송하고 그 데이터를 전달받아 해석하고 데이터베이스에 저장하고 이런 작업 자체가
  불가능했다. 
- **그럼 static 파일에 대한 요청이 아니라, 추가적인 연산이나 처리가 필요한 다른 일반적인 요청은 어떻게 처리할까. 이 부분을 우리에게 익숙한 방식으로 처리한다.**
  - **해당 요청을 nginx와 같은 web server에서 먼저 받고 -> 그대로 django server에 전달시키고 django는 마치 해당 요청을 직접 받은 것처럼 처리하면 된다. 그리고 그 결과를 nginx가 전달받아서
    client에게 되돌려준다.**
  - **그리고 이러한 구조에서 nginx를 django와 연동해서 사용할 수 있도록 해주는 프로그램이 gunicorn이라는 프로그램이다.** gunicorn은 단순히 nginx와 django를 연동해서 사용하기 위한 목적만 있는 것은 아니다. 연동되어있는 django 프로세스를 계속해서 모니터링하고 관리해준다. 이러한 과정에서 django가 안정적으로 운영되기 때문에 고가용성을 도모할 수 있다. 더불어 하나의 django가 아닌 여러개의 django 프로세스를 생성하고 관리할 수 있어서 주어진 컴퓨팅 파워를 최대한 활용할 수 있게끔 해준다.

- 이제 이러한 구성이 실제로 가능해지게끔 하나하나 작업을 해보자.


### 실습하기
- 먼저 EC2 인스턴스, Ubuntu 내부에 있는 env라는 가상환경으로 들어가준다. (EC2에 접근한 터미널 상태에서 진행하기)
```terminal
cd env
source bin/activate
```
- 그리고 source bin/activate로 가상환경을 활성화해준다. Linux는 Mac과 마찬가지로 bin이라는 디렉터리 내부에 activate가 있다. 
- 이제 env라는 가상환경 내부에 django 프로젝트를 넣어줘야 하는데, 이 때 우리가 개발한 django 프로젝트를 Github에 올린 다음 -> 해당 코드를 clone해서 해도 되지만, 일단은 django 샘플 코드를 바탕으로 실습을 해보자. 해당 코드에서 다른 점은, scripts라는 디렉터리에 django와는 관련이 없지만 서비스 배포 과정에 필요한 코드들을 넣어두었다. nginx 실습에 필요한 설정 파일과 guicorn를 실습하는데 필요한 gunicorn.service라는 파일이 추가되어 있다. 

- 그래서 해당 github repository에서 초록색 Code 버튼을 누르고 https 주소를 복사하고 -> env로 들어와 있는 상태의 터미널에서 
```terminal
git clone 복사한주소
```

- 해당 명령어를 입력해준다. 그러면 env 디렉터리 내부에 복제된 것을 확인할 수 있다. cd 명령어로 해당 django 프로젝트에 들어가자. 그 상태에서 해당 프로젝트를
```terminal
python manage.py runserver
```

- 다음 명령어로 실행시켜보자. 이렇게 python development 모드의 server를 구동시키게 되면 우리에게 익숙한 메세지가 뜬다. 
- **그리고 이제 브라우저에 localhost:8000 이렇게 입력하는 게 아니라 -> 우리의 EC2 인스턴스의 IP인 퍼블릭 IPv4 주소를 복사해서 -> 브라우저에 IP주소를 붙여넣은 다음 :8000을 붙여주면 된다.**
  - ex) 13.xxx.xxx.xx:8000 이렇게 브라우저에 입력.

- 그런데 아직 사이트에 연결할 수 없다고 나온다. 이는 우리가 별도로 설정하지 않으면 기본적으로는 localhost에서만 django server에 접속할 수 있기 때문에 server를 종료한 다음, 
```terminal
python manage.py runserver 0:8000
```

- runserver 다음에 한칸띄고 0:8000이라고 입력해서 -> host binding을 통해서 모든 IP 대역에서 django server에 접속할 수 있게끔 변경해주어야 한다.

- 하지만, DisallowedHost at / 이라는 에러가 뜨게 된다. 허용되지 않은 host 주소로 /라는 url pattern를 요청했다고 나온다. 그러면서 우리의 EC2 인스턴스의 IP 주소를 ALLOWED_HOSTS라는 변수에다가 추가해줘야 할 것 같다고 뜬다. 이는 django가 host header attack이라는 공격을 막기 위해서 host 주소를 검증하는 과정에서 발생한 에러이다. 자세한 내용은 django 공식 문서 Host header validation이라는 문서와 ALLOWED_HOSTS 항목을 참고해보자. 
  - 기본적으로는, 이 ALLOWED_HOSTS라는 변수에 등록되어있는 IP 주소나 도메인에서만 웹 페이지가 서빙될 수 있게끔 해주는 안전 장치이다.

- **그래서 다시 server를 종료한 다음, vim 이라는 터미널에서 사용할 수 있는 메모장 프로그램을 사용해서 settings 모듈을 수정할 것이다.**

```terminal
vim 프로젝트이름디렉터리/settings.py
```


- vim 프로젝트이름디렉터리/settings.py -> 이렇게 터미널에 입력하면 -> settings 모듈의 내용이 터미널에 뜨게 된다. 즉, 해당 내용을 수정할 수 있는 메모장 프로그램인 vim이 열린 것이다.
  - vim은 기본적으로 command 모드와 insert 모드로 구분해서 사용하는데, 값을 입력하거나 수정하기 위해서는 반드시 i 키를 눌러서 -> insert 모드로 전환한 다음에 값을 입력하거나 수정해야 한다. 
    그리고 수정이 완료되었다면, esc 키를 눌러서 반드시 command 모드로 빠져나온 다음에 화살표 키나 j,k등의 키를 활용해서 커서를 이동할 수 있다.
  - 우리는 settings.py에서 커서를 내려서 ALLOWED_HOSTS라는 변수를 찾으면 된다.
  
- vim관련해서, command 모드에서 알파벳 gg를 누르면 -> 커서가 파일 맨 위로 이동하게 된다. shift + g를 눌러보면 -> 커서가 파일의 맨 하단으로 이동하게 된다. 그리고 command 모드에서 이동하고자 하는 라인의 숫자를 입력한 다음 gg를 입력해보자. ex) 28gg -> 그러면 바로 28번째 줄로 순간이동하게 된다.

- 이제 ALLOWED_HOSTS라는 리스트 변수에다가 커서를 둔 다음, i키 혹은 a키를 눌러서 값을 입력할 수 있는 insert 모드로 변경해준다. 그리고 이 리스트 내부에다가 우리의 EC2 인스턴스 IP 주소를 추가해주면 된다. EC2 인스턴스의 IP주소인 퍼블릭 IPv4 주소를 복사해 
```python
ALLOWED_HOSTS = ['13.xxx.xxx.xx'] 
```

- 이런식으로 문자열로 넣어주자. 그리고 insert 모드를 종료하기 위해 esc키를 누르고 command 모드를 빠져나온 다음 변경한 내용을 저장하기 위해 :wq 를 입력하고 엔터를 누르면, 저장과 동시에 vim이 종료된다.
- 이제 다시 터미널에서 python manage.py runserver 0:8000으로 서버를 실행시키면 우리의 프로젝트가 보이게 된다.
  - 문제는 우리가 이 터미널을 끄는 순간, django server도 shut down 되면서 서비스에 사용자들이 접근할 수 없게 된다. **그래서 runserver 명령어를 통해서 우리가 django server를 실행시키는 것이 아니라, django 프로세스 매니저를 통해서 django server를 대신 실행하게끔 만들고 -> 우리가 터미널을 종료하더라도 django가 알아서 항상 떠있게끔 만들어줘야 한다.**
  - 그래서 이제 우리가 직접 server를 구동시키는 것이 아니라, gunicorn이라는 프로그램을 사용해서 django server를 대신 구동하게끔 만들어주자.

* * *
### WSGI
- **gunicorn은 python을 위한 WSGI인데 Web Server Gateway Interface를 의미한다. gunicorn은 nginx와 같은 web server와 django라는 프로그램이 서로 소통할 수 있게끔 해주는 인터페이스이다. 즉, 브릿지 역할을 해준다.**
- 그래서 이러한 인터페이스를 사용하기 위해 gunicorn이라는 프로그램을 사용하는 건데 다른 프로그램도 있다. 이 인터페이스를 통해서 nginx와 django를 연동시킬 수 있는데 반대로 말하면 nginx라는 web server와 통신하는 인터페이스대로 동작하는 프로그램이라면 꼭 django가 아니더라도 python의 Flask처럼 다른 프로그램을 연동해서 사용할 수도 있다.
- 우리가 작업한 프로젝트 디렉터리 -> 프로젝트 이름의 디렉터리 내부에 wsgi.py라는 파일이 있다. 해당 파일이 있기 때문에 wsgi라는 인터페이스를 사용할 수 있고 그 결과, gunicorn이라는 인터페이스의 구현체를 통해서 web server와 우리의 django 프로그램이 서로 통신할 수 있게 되는 것이다.
- 이러한 gunicorn를 사용하기 위해서는, django 프로젝트 루트 디렉터리에 들어간 터미널 상태에서
```terminal
gunicorn 프로젝트이름으로된디렉터리.wsgi --bind 0:8000
```

- gunicorn 한칸 띄고 wsgi.py가 있는 경로를 점을 기준으로 적어주면 된다. 그리고 gunicorn를 사용할 때도 host binding를 해줘야 하는데 --bind라고 적고 한 칸 띄우고 0:8000 이라고 적어주면 된다. -> 그러면 django server가 gunicorn를 통해서 간접적으로 대신 실행이 된다. 브라우저를 확인하면 정상적으로 서비스가 서빙되는 것을 확인할 수 있다.
  - **그런데 우리가 runserver로 직접 server를 구동시켰을 때는 색깔이 적용되었는데 지금은 되지 않는다. 개발자도구를 키고 Network를 선택하고 새로고침을 해보면 style.css 2개가 빨간색으로 에러가 발생한 것을 확인해볼 수 있다. 우리가 작성한 CSS 파일들이 import가 되지 않았다는 것이다.**
  - **static파일과 관련된 것들은 nginx가 직접 담당해서 서빙할 것이다라고 했는데, 지금 링크에 실패한 CSS 파일을 비롯해서 static 파일이 정상적으로 연동되려면 nginx까지 우리가 설정을 해줘야만 한다. 참고로 django development 모드의 server를 사용했을 때처럼 django 하나만 사용해도 static 파일을 서빙하는 게 불가능한 것은 아니지만, nginx와 같은 web server가 static 파일 서빙과 관련해서는 성능이 훨씬 좋기 때문에 -> production 환경에서는 nginx와 django를 같이 섞어서 연동해서 쓰는 게 일반적이다. (CSS파일, 사진 파일, javascript 파일 등등)** 

* * *
- **동작하고 있던 gunicorn은 일단 컨트롤 + c로 종료시키고, nginx를 설정해보자.**
- nginx의 설정파일들이 기본적으로 구성되어있는 형태가 있다.
  - 모든 설정의 베이스가 되는 nginx.conf 파일이 있다.(configuration) 
```terminal
sudo apt-get install nginx
```

- Ubuntu에서 이러한 명령어를 통해서 nginx를 설치하게 되면 -> /etc/nginx/ 라는 경로에 nginx가 설치된다. 
```terminal
cd /etc/nginx/
```

- 이렇게 cd 명령어로 해당 경로로 들어가 준다. 이 상태에서 ls 명령어로 내부를 확인해보면, nginx.conf 라는 모든 설정의 베이스가 되는 기본 설정 파일이 있는데 
```terminal
vim nginx.conf
```

- 이렇게 vim으로 내용물을 확인해보면 -> 다양한 설정이 복잡하게 작성되어있다. 이 파일에서는 nginx라는 web server 자체에 대한 설정을 구성하는데, 사용자가 접속했을 때 server의 로그는 어디에다 남길지, 에러 로그는 어디에다 남길지, 최대 몇개의 커넥션을 허용할지 등을 설정할 수 있다.
- 그리고 설정파일의 하단부분을 보면, include라는 키워드를 통해서 다른 경로에 있는 별도의 설정 파일의 내용을 포함시켜 사용하겠다 라고 코드가 작성되어 있다. 그래서 /etc/nginx/conf.d/*.conf; 이렇게 이름이 뭐가 되었든 .conf로 끝나는 설정 파일은 내가 다 가져다 쓰겠다 라고 하는 것이다.
- 그리고 그 다음줄에는, include /etc/nginx/sites-enabled/*; -> 이렇게 sites-enabled라는 디렉터리 내부에 있는 이름이 뭐가 되었든 설정 파일이 있기만 한다면 그걸 가져다 쓰겠다라는 의미가 된다.
  - 여기서 우리가 서비스를 배포하는 과정에서 고쳐야할 부분은, sites-enabled 정도이다.

- 우리의 웹사이트를 서빙하기 위한 nginx 설정을 구성하는 방법은 -> 하나의 nginx web server내에서는 여러 개의 웹사이트를 운영할 수 있는데 -> sites-available이라는 디렉터리 내부에 해당 web server에서 서빙하고자 하는 여러 웹사이트들에 대한 설정을 사이트별로 구분해서 별도의 파일로 작성한다. ex) 하나의 web server에서 likelion.com이나 example.com도 서빙할 수 있다. 즉, 서빙가능한 사이트의 목록을 의미한다.
- 이중에서 실제로 어떤걸 연동해서 사용할지, 하나만 사용할지 아니면 여러개를 사용할지에 대한 내용을 sites-enabled 라는 디렉터리에 구성하는데, 주로 여기에서는 Linux의 symbolic link라는 것을 활용한다. Window 운영체제의 바로가기라고 생각하면 된다.
  - **사이트 자체에 대한 설정은 sites-available 디렉터리 내부에 넣어두고 실제로 적용하고자 하는 대상은 sites-enabled에 symbolic link의 형태로 바로가기의 형태로 지정해주는 것이다.**
  - **그래서 sites-enabled에 default라는 이름을 가진 바로가기를 만들었는데 이 바로가기가 가리키는 대상은 sites-available 디렉터리 내부에 있는 likelion.com이라는 설정 파일이다.(예를 들어..) 그 결과, 최종적으로는 nginx.conf 파일은 -> sites-enabled안에 있는 모든 걸 포함하니까 default를 포함하게 되고, -> default가 sites-available 안에 있는 likelion.com을 가리키니까 -> 최종적으로는 likelion.com이라는 설정 파일이 nginx.conf 파일에 포함이 된다고 보면 된다.**

* * *
- 지금까지 켜놓았던 vim에서 esc -> :q를 눌러서 vim으로 nginx.conf 파일을 열었던 것을 다시 닫아준다. 그리고나서
```terminal
cd sites-available/
ls
```

- 이렇게 경로를 이동하고 ls를 입력하면 -> default라는 이미 작성되어있는 설정파일이 있다. **이제 sites-available안에 heestagram.com이라는 사이트 설정 파일을 새롭게 만들어줄건데 vim를 사용하자.**
- 그리고 /etc 처럼 맨 앞이 /로 시작하는 디렉터리의 경우에는 관리자 권한이 필요하기 때문에 
```terminal
sudo vim heestagram.com
```

- 이렇게 작성해주어야 한다. vim를 열었으면 i키를 눌러서 insert 모드로 전환한 다음 여기에 nginx설정을 적어주면 된다. 그 설정에 대한 내용이 likelion 공유된 github에 django-sample이라는 repository에서 scripts 디렉터리를 눌러보면 -> heestagram.com이라는 파일이 있다. 해당 파일의 내용물을 복사해서(마지막 중괄호까지 다 복사하기) vim에 붙여넣기. 
  - 그리고 세번쨰 줄에 있는 server name을 -> 우리의 EC2 인스턴스 IP 주소를 작성해주자.

```vim
...
server_name 13.xxx.xxx.xx;
```

- 이렇게 작성해주자. 해당 vim 안에 있는 코드들에 대한 설명은 다음과 같다.
  - 80번 포트에서 web server가 동작할 것이고, 해당 server는 우리가 입력한 IP에서 동작하고 location = /favicon.ico 즉, 사용자가 /favicon.ico로 딱 끝나는 url pattern으로 요청하게 되면 해당 요청에 대한 기록은 server에 별도 로그로 남기지 않았으면 좋겠어 라는 것. location = 이 아니라 location/static -> /static으로 시작하는 url pattern의 경우에는 alias /... 더 긴 경로가 나오는데, 우리의 django 프로젝트 내부에 있는 static_files라는 디렉터리 내부를 뒤져서 전달해주었으면 좋겠다는 것. 
  - 즉, 만약에 사용자가 heestagram.com/static/xxx.jpg 이러한 파일을 요청했다면 -> static_files 내부에서 xxx.jpg라는 파일을 찾아서 서빙해줘 라는 설정이 된다.
  - 마찬가지로 location /media -> /media로 시작하는 url pattern의 경우에는 django 프로젝트 내부에 있는 media라는 디렉터리 내부에서 파일을 찾아서 서빙해줘 라는 설정이 된다.
  - location / -> 즉, 그 외에 나머지 /로 시작하는 request는 nginx가 받았던 다양한 파라미터를 전부 포함시킨 상태로 proxy_pass http://127.0.0.1:8000; -> 즉, 현재 서버 내부에 localhost 환경에 동작하고 있는 8000번 포트로 전달시켜줘, 그런데 이 8000번 포트에 우리의 django server가 동작하고 있기 때문에 -> /static으로 시작하는 url pattern과 /media로 시작하는 url pattern을 제외한 나머지 모든 url pattern은 localhost:8000으로 전달해줘, 즉 우리의 django server로 전달해줘, 정확하게는 django server를 대신 실행시켜주는 gunicorn으로 전달해줘 라는 설정이 된다.

- 그래서 이렇게 heestagram.com이라는 파일을 vim으로 작성했다면 esc누르고 insert 모드에서 command 모드로 빠져나온 다음 :wq로 저장과 동시에 종료를 해주자.

* * *
- 이제 sites-available이 아니라 sites-enabled로 이동할 것인데, 
```terminal
cd ..
cd sites-enabled/
```

- 이렇게 한 디렉터리로 나와서 sites-enabled로 들어오자. 그래서 해당 위치에서 ls를 했을 때 역시 default라는 것이 있다.
```terminal
ls -la
```

- **해당 명령어로 상세하게 내용물을 살펴보면, 이 default라는 것은 단순한 파일이 아니라 symbolic link라고 해서 sites-available 안에 있는 default를 가리키는 바로가기 같은 것이다 라고 표시되고 있다. 이제 우리는 default라는 symbolic link가 sites-available안에 있는 default를 가리키게끔 하는 것이 아니라 -> 우리가 방금 추가해준 heestagram.com를 가리키게끔 변경해주면 된다.**
- 그래서 우리는 symbolic link를 지운 다음, 다시 만들어줄 것이다.

```terminal
sudo rm -rf default
```

- **그래서 관리자 권한이 필요하기 때문에 위의 코드를 적어줘서 기존에 있었던 default symbolic link를 지워준다.** ls를 입력하면 아무것도 없게 된다.

```terminal
sudo ln -s /etc/nginx/sites-available/heestagram.com default
```

- **그리고 다시 symbolic link를 만들어준다. 바로가기로 가리키고자 하는 대상의 경로를 작성해줘야 한다. 경로를 작성하고 바로가기의 이름은, 한 칸 띄우고 default라고 적어준 것이다.**
  - 이렇세 설정한 다음 다시 ls를 입력해보면 default가 생겨있는 것을 확인할 수 있다. ls -la를 해보면 default라는 symbolic link가 sites-available/heestagram.com 을 가리키게 된다.

- 지금처럼 nginx설정을 바꾼 다음에는, 반드시 nginx를 재시작해줘야 한다. 재시작뿐만 아니라 nginx와 관련된 다양한 명령어는 다음과 같다.
```terminal
# ngnix 시작
sudo service nginx start

# ngnix 재시작
sudo service nginx restart

# ngnix 정지
sudo service nginx stop

# ngnix 상태 확인
sudo service nginx status
```

- 그래서 sites-enabled로 들어와 있는 상태에서 sudo service nginx status라고 입력하면, 현재 nginx가 정상적으로 동작하고 있다는 것을 확인할 수 있다. 그리고 그 상태에서 알파벳 q를 누르면 종료되면서 다시 ubuntu@IP 주소 경로가 뜨면서 뭔가를 입력할 수 있는 상태로 돌아온다.

* * *
- 이렇게 nginx의 설정을 바꿨으니까 재시작을 해주자. sites-enabled로 들어와 있는 상태에서 sudo service nginx restart 이렇게 입력하면 재시작이 된다.
  - 그리고 nginx가 static 파일에 대한 요청을 받았을 때 해당 static 파일을 어딘가에서 찾아서 검색해서 서빙을 할 수 있게끔 해줘야 하는데 

```terminal
cd ../sites-available/
vim heestagram.com
```

- **이렇게 sites-available 안에있는 heestagram.com 설정 파일에서 나와있듯이, 우리는 /static으로 시작하는, 즉 static 파일에 대한 요청이 있으면 static_files라는 디렉터리에서 찾아줘 이렇게 설정을 작성해줬었고, 그래서 사용자가 요청한 static 파일들이 static_files 라는 디렉터리 내부에 있어야 한다는 것이다.**

- **이를 위해서 -> 우리가 개발 단계에서 app 단위로 쪼개어 관리했던 static 디렉터리들을 -> nginx와 연동하기 위해 하나의 static 디렉터리로 합쳐줘야 한다.**

- 위에 vim에서 :q로 종료하고 
```terminal
cd
cd env/django-sample/
```

- cd 이렇게 입력해서 홈 디렉터리로 이동한 다음, django 프로젝트로 이동하고 

```terminal
vim heestagram/settings.py
```

- **settings.py 모듈을 열어주자. 그리고 DEBUG = True 라고 되어있는 부분을 False로 바꿔준다. 이 DEBUG라는 변수가 False가 되어야 -> django server가 development 모드가 아닌 production 모드로 동작할 수 있다.**
  - **django server를 production 모드로 동작한다는 것은 이제 static 파일에 대한 처리를 django가 직접 처리하는 것이 아니라 nginx와 같은 web server가 대신 처리할거야 라는 말이 된다.**

- 이렇게 수정한다음 esc를 누르고 command 모드로 빠져나온 다음, shift + g를 눌러서 파일의 맨 하단으로 이동하고 -> django의 static과 관련된 변수들이 모여있는 
```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    BASE_DIR / 'static',
]
``` 

- 이 부분 하단에 추가를 해준다.

```terminal
STATIC_ROOT = BASE_DIR / 'static_files'
```

- **이렇게 작성해주면 된다. 이제 우리가 django의 기능을 활용해서 app 단위로 구분되어있는 static 디렉터리들을 하나로 합칠 때 -> STATIC_ROOT라는 변수를 참조해서 static_files라는 디렉터리에다가 합치게 되는 것이다.**

* * *
- esc를 눌러서 insert 모드를 빠져나온 다음, :wq로 저장 및 종료를 해준다. 이렇게 settings 모듈을 수정해준 다음, env/django-sample/의 경로로 들어와있는 터미널 상태에서 
```terminal
python manage.py collectstatic
```

- **다음과 같은 명령어를 입력하는 순간, 모든 static 디렉터리가 하나로 합쳐지면서 static_files라는 디렉터리가 생성되었다는 메세지가 뜨게 된다.** 실제로 ls를 확인해보면, 
  - static_files 가 생성된 것을 확인할 수 있다.

```terminal
ls -la static _files/
```

- 이렇게 입력하면, static_files 디렉터리 내부가 어떻게 구성되어 있는지도 확인할 수 있다.

* * *
- **그럼 이제 nginx설정도 다 했고, django에서도 준비가 되었으니까 nginx와 django가 연동되려면 우리가 아까 껐던 gunicorn를 다시 켜줘야 한다.**
```terminal
gunicorn heestagram.wsgi --bind 0:8000
```

- 이렇게 입력한 다음, gunicorn를 실행시켜주고 웹페이지를 새로고침해도 여전히 여전히 style.css가 링크되지 않는 것을 확인할 수 있다.
  - **이는 우리가 IP 주소:8000번의 형태로 입력했기 떄문인데 -> 즉, gunicorn이 동작시키고 있는 django server에 직접 접근하게 된 것이고 이 경우 django는 static 파일을 서빙할 능력이 없기 때문에 여전히 링크가 불가능 한 것을 확인할 수 있다.**
  - **이제 IP주소:8000이 아니라 -> 그냥 IP주소만 입력하고 엔터를 치는 순간 css파일들이 적용되고 스타일이 입혀진 것을 확인할 수 있다.**

- 우리가 브라우저에 IP주소만 입력하고 엔터를 누른다는 건, 브라우저가 자체적으로 80포트를 붙여준다고 배웠다. 그리고 nginx설정에서 listen 80를 했었다. 그래서 우리가 IP주소로 접속하면 HTML 파일을 응답으로 받았고 브라우저는 이를 해석해서 웹 페이지로 보여주게 되는데, 브라우저가 HTML 파일을 해석하다보니 head element 내부에 다양한 링크가 있었고 그 중에 link ... href=static 이러한 코드를 읽는 순간 -> 추가적인 데이터 통신이 이루어져야 한다는 것을 알게 된다.
  - 그래서 개발자도구 Network 탭에 있는 style.css를 더블 클릭해보면 알 수 있듯이 CSS파일을 요청하면 정상적으로 받을 수 있게 되었다. 그리고 media 파일도 정상적으로 출력되는 것을 확인할 수 있는데, 이 media 파일 역시 /media로 시작하는 모든 url pattern은 nginx가 처리해줘 이렇게 우리가 설정했기 때문에 우리가 볼 수 있는 이미지 파일도 django가 직접 서빙한 게 아니라 nginx가 자체적으로 서빙한 것이다.

- **그리고 우리가 브라우저에 IP주소만 입력했을 때 HTML 페이지를 볼 수 있었던 이유는 -> url pattern이 /static으로 시작하지도 않고 /media라고 시작하지도 않는, 다른 url pattern은 모두 gunicorn 한테 전달해줘 -> 그리고 gunicorn은 django server와 연동되어 있으니까 django server가 게시물을 조회해서 template을 통해서 HTML 페이지를 완성한 다음에 response로 응답해줬기 때문에 우리가 페이지를 볼 수 있게 되는 것이다.**


### HTTP Request -> nginx라는 web server(static파일 서빙/css, media, js파일 등등) -> guicorn이 django server와 nginx 연결 -> django server(template의 HTML 페이지 response) -> 데이터베이스 server(랜더링하는데 필요한 데이터를 해당 서버에서 갖고 있다가 보내줌)

* * *
- 참고해볼 자료 : https://velog.io/@sj950902/DJANGO%EC%99%80-WSGI%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90
- https://likelion.notion.site/Vim-218aa43cf8aa4b3bacfbfda1651554c8
