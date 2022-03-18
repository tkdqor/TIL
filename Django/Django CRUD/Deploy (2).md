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



- vim 프로젝트이름디렉터리/settings.py -> 이렇게 터미널에 입력하면 -> settings 모듈의 내용이 터미널에 뜨게 된다. 즉, 해당 내용을 수정할 수 있는 메모장 프로그램인 vim이 열린 것이다.
  - vim은 기본적으로 command 모드와 insert 모드로 구분해서 사용하는데, 값을 입력하거나 수정하기 위해서는 반드시 i 키를 눌러서 -> insert 모드로 전환한 다음에 값을 입력하거나 수정해야 한다. 
    그리고 수정이 완료되었다면, esc 키를 눌러서 반드시 command 모드로 빠져나온 다음에 화살표 키나 j,k등의 키를 활용해서 커서를 이동할 수 있다.
  - 우리는 settings.py에서 커서를 내려서 ALLOWED_HOSTS라는 변수를 찾으면 된다.
  
- vim관련해서, command 모드에서 알파벳 gg를 누르면 -> 커서가 파일 맨 위로 이동하게 된다. shift + g를 눌러보면 -> 커서가 파일의 맨 하단으로 이동하게 된다. 그리고 command 모드에서 이동하고자 하는 라인의 숫자를 입력한 다음 gg를 입력해보자. ex) 28gg -> 그러면 바로 28번째 줄로 순간이동하게 된다.

- 이제 ALLOWED_HOSTS라는 리스트 변수에다가 커서를 둔 다음, i키 혹은 a키를 눌러서 값을 입력할 수 있는 insert 모드로 변경해준다. 그리고 이 리스트 내부에다가 우리의 EC2 인스턴스 IP 주소를 추가해주면 된다.


9:50



* * *
- 참고해볼 자료 : https://velog.io/@sj950902/DJANGO%EC%99%80-WSGI%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90
- https://likelion.notion.site/Vim-218aa43cf8aa4b3bacfbfda1651554c8
