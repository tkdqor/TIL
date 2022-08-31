## Docker로 django와 nginx 띄우기
- [참고 영상](https://www.youtube.com/watch?v=GpvfIs_ewGY)
- [참고 블로그](https://cholol.tistory.com/489)

- **Docker로 진행하게 되면, aws ec2에 직접 django와 nginx를 설치하는 것이 아니라, docker를 이용해 그 안에 django 컨테이너와 nginx 컨테이너를 만들고 이미지를 빌드해서 올리게 된다.**
  - **그리고 django 컨테이너와 nginx 컨테이너를 동시에 관리하기 위해서 docker-compose 라는 걸 사용해볼 수 있다.**
  - 이렇게 진행하면 aws ec2 우분투가 아닌 다른 서버에서도 손쉽게 설치를 진행할 수 있게 된다.

<br>

### django 컨테이너 및 이미지 생성하기
- **처음에 EC2에서 mkdir docker-server 명령어로 docker-server라는 폴더를 생성한다. cd를 입력하고 해당 폴더에 들어가면, 그 위치가 앞으로 docker-compose의 루트가 될 경로이다. 해당 위치에서 django와 nginx 컨테이너를 만들 예정이다.**

- 일단 docker-server 디렉터리 내부에서
```terminal
git init . 
git remote add origin 레포지토리 주소
git pull origin 브랜치 이름
```

- 이렇게 우리의 django 앱을 가져오자. 브랜치 이름으로 pull를 진행하면 main 내용은 가져오지 않게 된다.

<br>

- **EC2에 docker 설치하기**
```terminal
curl -fsSL https://get.docker.com/ | sudo sh
```

- 해당 명령어로 EC2에 docker를 설치하자. 그리고

```terminal
sudo usermod -aG docker $USER

docker -v
Docker version 20.10.17, build 100c701
```

- 해당 명령어로 현재 유저한테 도커와 관련된 권한을 허가해준다. 그리고 설치가 되었는지 확인하기 위해 docker -v 명령어를 입력해서 버전을 확인하면 된다.

<br>

- **EC2 docker-server 디렉터리 내부에 Dockerfile를 생성하기 위해, 로컬에서 Dockerfile를 생성하자.**
  - **앞으로 로컬에서 도커 관련 파일들을 작성하고 => github에 push => ec2에서 github pull => 도커 이미지 생성 및 컨테이너 build (docker-compose를 이용해 각 컨테이너 연결)를 진행할 것이다.**

- 그래서 일단, 로컬에서 프로젝트 루트 디렉터리 내부에 Dockerfile을 생성한다.

```dockerfile
FROM python:3.8

ENV PYTHONUNBUFFERED 1

RUN apt-get -y update
RUN apt-get -y install vim

RUN mkdir /srv/docker-server
ADD . /srv/docker-server

WORKDIR /srv/docker-server

COPY ./Pipfile /srv/docker-server/Pipfile
COPY ./Pipfile.lock /srv/docker-server/Pipfile.lock

RUN pip install pipenv
RUN pipenv install
RUN pipenv install uwsgi

EXPOSE 8000
CMD ["pipenv", "run", "python", "manage.py", "runserver", "0.0.0.0:8000"]
```

- FROM은 도커 이미지 환경을 정해주는 것이다. ENV는 환경변수 설정이고, RUN으로 도커 이미지안에서 여러가지 설정 명령어를 입력한다. 
- **RUN mkdir /srv/docker-server는 생성되는 도커 컨테이너 내부의 srv 디렉터리 안에 docker-server 디렉터리를 생성한다는 의미이다. srv는 도커 컨테이너의 루트 디렉터리라고 볼 수 있다.**
  - 즉, 도커를 실행하면 기본적으로 컨테이너안에 srv 폴더가 생기고 그 안에 docker-server라는 디렉터리를 만들어 우리의 django 앱 관련 코드들을 넣어주는 것이다.

- **pipenv의 경우, 여기서 관련 명령어를 입력해준다.** 
- EXPOSE 8000은 docker 컨테이너 내부에서만 해당 포트를 연결할 수 있게 해주는 것이다. CMD로 django 컨테이너를 띄울 때 가상환경을 실행해주는 것이다.

<br>

- **django 컨테이너 생성 및 이미지 빌드하기** 

```terminal
docker build -t django . 
```

- 이렇게 입력하면 django라는 이름의 태그를 주고, . 이라고 해서 현재 위치에서 도커 이미지를 만드는 것이다. 즉, 지금 상황에서는 EC2의 docker-server에 들어가 있는 위치이다.
  - **도커 이미지를 만드는 건 “build” 이고  /  이미지를 실행시키는 건 “run” 이라고 생각하면 된다.**

- 해당 명령어 입력 후, 
```terminal
docker image ls
```

- 이렇게 입력하면
<img width="575" alt="image" src="https://user-images.githubusercontent.com/95380638/179742457-8eeb9b62-5ad6-4c7e-82d3-a2d009dcbdf5.png">

- 다음과 같이 django 이미지가 생성된다. 

```terminal
# 도커 이미지 지우는 명령어
docker image rm –force 이미지id

# 도커 이미지 실행하기
docker run 이미지태그이름

# 도커 컨테이너 멈추기
docker stop 컨테이너id

# 도커 이미지 데몬 형태로 실행하기
docker run -d 이미지태그이름

# 실행중인 도커 이미지 확인하기
docker ps
```
- **이렇게 이미지를 만들고 지울 수 있고, 이미지를 실행해보면 우리가 Dockerfile에서 CMD를 넣어줬기 때문에 자동으로 가상환경이 활성화된다. 데몬 형태로 실행하면 터미널에서 실행되는 것이 아니라, 백그라운드에서 실행된다.**

- 이미지를 실행한 상태에서, 실제로 브라우저에 ip주소를 입력해 확인해보자. aws에서 보안 그룹 규칙을 추가해서 “사용자 지정 TCP”로 / 포트 범위는 8000으로 / Anywhere IPv4 / 이렇게 설정하고 규칙을 저장한다.
- **그리고 우리가 docker run를 할 때, 우분투 서버에 있는 포트랑 / 도커 컨테이너에 있는 포트랑 매핑해주는 게 필요하다.** 
  - 그래서 실제로 우분투 서버 8000번으로 들어오는 포트를 → 도커의 8000번 포트랑 연결한다는 옵션을 넣어주고 명령어를 입력한다. 

```terminal
docker run -p 8000:8000 -d django 
```

- **이렇게 포트를 연결해서 실행시키고, 웹 브라우저에서 aws ec2의 ip 주소:8000 이렇게 접속해보면 화면이 정상적으로 뜨는 것을 확인할 수 있다.**

<br>

- **여기까지 그냥 EC2에 django를 설치한 것이 아니라, 도커 위에 올라가 있는 django를 실행시켜 본 것이다.**

<br>

<hr>

### nginx랑 django랑 연결시키기 위한 uwsgi 설정하기
- **로컬에서 uwsgi.ini 파일 생성하기**

```ini
[uwsgi]
socket = /srv/docker-server/django.sock
master = true

processes = 1
threads = 2

chdir = /srv/docker-server
module = config.wsgi

logto = /var/log/uwsgi/uwsgi.log
log-reopen = true

vacuum = true
chmod-socket=666
```

- **socket으로 nginx와 연동할 것이기 때문에 해당 경로와 nginx.conf 파일에 설정된 socket 경로가 일치해야 한다.**
  - module = config.wsgi 이건 config 부분이 프로젝트 이름이다. 루트 프로젝트 디렉터리 내부에 있는 wsgi.py를 의미한다.
  - logto = /var/log/uwsgi/uwsgi.log 이건 로그를 남기기 위한 경로이다.
  - vacuum = true


- 그 다음에 Dockerfile에서 EXPOSE 8000와 CMD 부분을 주석처리 해주자. 이젠 django 앱의 가상환경을 바로 활성화시키기 않을 것이기 때문이다.
  - **그리고 Dockerfile에 추가로 RUN pipenv install uwsgi를 추가해준다. 즉, django 이미지 만들 때 uwsgi를 같이 설정해주는 것이다.**

<br>

<hr>

### EC2에 docker-compose 설치하기
- EC2 터미널에서

```terminal
sudo curl -L https://github.com/docker/compose/releases/download/1.25.0-rc2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

- 이렇게 입력하고 docker-compose 설치하기.

```terminal
sudo chmod +x /usr/local/bin/docker-compose
```

- 여기서도 마찬가지로 권한을 줘야 한다. 
- 다 입력하고 나서 우분투 터미널에 docker-compose를 입력해보면, 사용할 수 있는 명령어들을 출력해준다.

<br>

<hr>

### ngnix를 설정하기
- **nginx를 설정하기 위해 먼저 로컬의 루트 디렉터리에 ngnix라는 이름의 디렉터리를 만들자.**
- 그리고 nginx도 또 하나의 컨테이너 및 이미지를 빌드해야 하기 때문에, nginx 디렉터리 내부에 Dockerfile를 만들고 config 파일을 2개 만들어야 한다.

- 먼저 nginx.conf 파일은,

```conf
user root;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 1024;
    # multi_accept on;
}

http {

    ##
    # Basic Settings
    ##

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;
    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # SSL Settings
    ##

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    ##
    # Logging Settings
    ##

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Gzip Settings
    ##

    gzip on;
    gzip_disable "msie6";

    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    ##
    # Virtual Host Configs
    ##

    # include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

- **다음과 같이 입력해준다. 마지막에 include /etc/nginx/sites-enabled/*; 이 코드로 sites-enabled 내부에 있는 conf 파일로 연결해준다.**

- **그리고 nginx 폴더에 nginx-app.conf 파일도 생성한다.**

```conf
upstream uwsgi {
    server unix:/srv/docker-server/django.sock;
}

server {
    listen 80;
    server_name localhost;
    charset utf-8;
    client_max_body_size 128M;

    location / {
        uwsgi_pass      uwsgi;
        include         uwsgi_params;
    }

    location /media/ {
        alias /srv/docker-server/.media/;
    }

    location /static/ {
        alias /srv/docker-server/.static/;
    }
}
```

- **해당 파일에서는, 80 포트로 들어오는 요청을 처리한다. 그리고 location / 로 요청이 올 때는 --> uwsgi로 연결하고 --> 위에 정의한 upstream uwsgi로 우리가 uwsgi.ini에서 설정한 socket 경로를 적어줘서 nginx와 uwsgi가 연결이 될 수 있도록 해준다.**

- 마지막으로는, nginx 폴더안에 Dockerfile를 생성한다.

```dockerfile
FROM nginx:latest

COPY nginx.conf /etc/nginx/nginx.conf
COPY nginx-app.conf /etc/nginx/sites-available/

RUN mkdir -p /etc/nginx/sites-enabled/\
    && ln -s /etc/nginx/sites-available/nginx-app.conf /etc/nginx/sites-enabled/

# CMD ["pipenv", "run", "nginx", "-g", "daemon off;"]
CMD ["nginx", "-g", "daemon off;"]
```

- nginx 컨테이너 및 이미지를 생성하기 위함이다. ngnix를 최신버전으로 설정하고, 우리가 생성한 nginx.conf를 도커 컨테이너 내부 nginx.conf로 옮겨주고, nginx-app.conf 파일을 sites-available 디렉터리 내부로 복사한다.
- RUN mkdir -p /etc/nginx/sites-enabled/\ && ln -s /etc/nginx/sites-available/nginx-app.conf /etc/nginx/sites-enabled/ 
  - **해당 코드는, sites-available이라는 폴더를 사용해서 링크를 걸어서, sites-enabled에서 nginx-app.conf 파일을 사용할 수 있도록 하는 것이다.**

- **여기까지 진행한 다음, 이제 nginx만 따로 도커 이미지를 만들어서 띄워보자.**
  - 이렇게 하려면 nginx 폴더 안에 있는 Dockerfile에 들어가서 EXPOSE 80를 추가로 입력
  - 그리고나서 터미널에서 nginx 폴더로 들어와서 Dockerfile 이 ls로 보이는 위치에서 docker build -t nginx . ⇒ 이렇게 입력하기

- 그러면 이제 docker image ls 를 입력했을 때 이미지가 생성된 걸 확인할 수 있고, 
```terminal
docker run -d -p 80:80 이미지이름
```

- 이렇게 포트를 연결해서 실행시켜주자. 그러면 docker ps 를 입력했을 때 nginx가 뜨게 된다. 
- **이제 실험하기 위해 aws에 가서 보안그룹 편집을 누르고 HTTP를 선택하고 위치무관을 하면 자동적으로 80포트가 열리게 된다(HTTP가 80포트이다)**
- 그리고나서 ip주소만 브라우저에 입력해보면,
<img width="623" alt="image" src="https://user-images.githubusercontent.com/95380638/179748700-f5bc3353-6e54-40b4-bcca-1739af79181f.png">

- **다음과 같이 nginx라고 뜨는 것을 확인할 수 있다. 여기까지 nginx가 도커로 뜨는 것을 확인된 것이다.**

<br>

<hr>

### Docker-compose 설정하기
- 로컬에서 docker-compose.yml 이라는 파일을 생성하기
- 이 파일로 전체 환경을 관리할 수 있고, django 컨테이너 및 이미지와 nginx 컨테이너 및 이미지를 관리할 수 있다.

```yml
version: '3'
services:

    django:
        container_name: django
        build: .
        image: docker-server/django
        restart: always
        command: >
          sh -c "pipenv run python manage.py collectstatic --no-input
          && pipenv run uwsgi --ini uwsgi.ini"
        volumes:
          - ./:/srv/docker-server
          - ./log:/var/log/uwsgi

    nginx:
        container_name: nginx
        build: ./nginx
        image: docker-server/nginx
        restart: always
        ports:
          - "80:80"
        volumes:
          - ./:/srv/docker-server
          - ./log:/var/log/nginx
        depends_on:
          - django
```

- services ⇒ 이 부분에다가 우리의 도커 이미지들을 적어주면 된다.
- build ⇒ Dockerfile이 있는 경로를 적어주면 된다.
- **ports ⇒ 이건 우리가 docker run할 때 -p 옵션으로 준 것 처럼, 외부에서 어떤 포트로 접근했을 때 도커 컨테이너의 어떤 포트로 응답할 것인지 설정해주는 것이다.**
- **volumes ⇒ 도커가 실행했다가 멈추면 기존의 우리가 세팅한 파일들 말고는 다 날아간다. 그걸 계속 가지고 싶다할 때 이 volumes 옵션을 주면 도커 이미지가 생성될 때 그 안에 있는 폴더랑 , 우분투에 있는 폴더랑 연결시켜서, 도커 이미지가 죽어도 파일이 계속 유지가 되게끔 설정하는 것이다.**
  - **:콜론을 기준으로 왼쪽이 우분투 ec2 디렉터리를 의미하고 오른쪽이 도커 컨테이너 내부 디렉터리를 의미한다.** 
  - **해당 volumes가 올바르게 설정되어있지 않으면, 게속해서 502 Bad Gateway 에러가 발생하면서 socket를 찾지 못하게 된다.**

- depends_on ⇒ 도커 컨테이너 2개를 동시에 띄운다고 했을 때, django 앱이랑 nginx랑 동시에 뜰 때, 순서상으로는 django앱이 먼저 뜨는 게 맞다. 왜냐면 nginx가 뜨기 위해서는, django 앱이 뜨고나서 wsgi 소켓을 물고 nginx가 떠야 되기 때문이다. 근데, nginx가 먼저 떠버리면 아직 django 앱이 실행 전 이기 때문에 소켓을 찾지 못한다. 그래서 자동적으로 nginx가 죽는다. 따라서 안정적으로 봤을 때 django 앱이 먼저 뜨고 nginx가 뜨는 것이 맞다. 이러한 설정을 해주는 부분이다.
  - DB 뜰때도 마찬가지이다. django 앱이랑 DB가 있을 때, DB가 먼저 뜨고 django 앱이 떠야지 연동이 된다. 만약 django 앱이 먼저 뜨고 DB가 뜨면 이 django가 죽게 된다. 오작동을 하게 된다. 그래서 이 depends_on 이라는 옵션으로 어떤 컨테이너를 먼저 띄울지 정할 수 있다.
  - 지금은 nginx보다 django가 먼저 떠야 한다고 설정해주자.

- image ⇒ 이 image라는 것은, docker-compose를 쓴다고 해서 2개의 이미지가 하나의 이미지로 만들어지는 것은 아니다. 도커 이미지는 따로따로인데, 동시에 실행시켜주고 관리할 뿐이다. 그래서 nginx의 이미지 이름을 뭘로할지, django앱의 이미지 이름을 뭘로할지 그냥 정하는 것이다.

- command ⇒ uwsgi는 django앱에서 이렇게 커맨드로 실행시켜줘야 한다. 그리고 django 배포 설정을 위해 pipenv run python manage.py collectstatic 명령어도 함께 적어준다. 2개의 명령어를 함께 입력하기 위해 > sh -c를 붙여주고 명령어 사이에 --no-input && 를 붙여준다.

- 여기까지 로컬에서 docker-compose.yml를 작성한 다음, ec2에서 git pull를 진행한다.

<br>

<hr>

### Docker-compose 실행하기
- 실행하기 전에, docker ps 명령어로 남아있는 컨테이너가 있는지 확인하고, docker image ls 명령어로 이미지가 남아있다면 -> docker image rm --force 이미지id 이미지id 이런식으로 이미지를 삭제해준다.
- docker-compose를 사용해서 도커 이미지들이 생성되는지 확인하기 위해 해당 과정을 진행하는 것이다. 

```terminal
docker-compose up -d --build 
```

- **해당 명령어로 데몬 형태로 docker-compose를 실행시킬 수 있다.(docker-server 디렉터리 안에 있는 상태에서 입력)**
  - --build로 이미지를 만드는 것이다. 
  - 이걸 입력하면 docker-compose.yml에 정의되어있는 순서대로 진행이 된다.
  - 이렇게 ec2에서 docker-server라는 디렉터리가 있고 / 그 안에 docker-compose.yml 파일이 있고 / 그리고 nginx라는 폴더가 있고 / docker-server안에 바로 django 앱과 관련된 파일들이 있게 된다.
  - **그래서 docker-compose.yml을 실행시키면 → nginx를 실행시키고(즉 도커 이미지를 만듬) → django 앱도 실행시킨다.**

```terminal
docker-compose ps
```

- 해당 명령어로 잘 수행되고 있는지 확인할 수 있다.
<img width="620" alt="image" src="https://user-images.githubusercontent.com/95380638/179752601-776f9f8c-fe4e-4cb9-9f95-19b936b137ed.png">

- 위와 같이 State가 Up이 아니라 Restarting이라면 문제가 있다는 뜻이다.
- **docker image ls 이 명령어를 입력하면, 다시 이미지들이 생성되었기 때문에 ==> docker-compose로 실행하면 → 도커 이미지를 각각 만들고나서 → 같이 띄워주는 게 docker compose의 역할이라는 것을 알 수 있다.**


```terminal
docker-compose down

docker-compose up -d –build 

docker-compose ps
```

- **무엇인가 오류를 수정하고 파일을 수정했다면 compose를 down 시키고 다시 build를 진행해야 한다.**
  - 그리고 docker-compose ps로 

<img width="616" alt="image" src="https://user-images.githubusercontent.com/95380638/179753221-3a8f97df-6642-40d2-8ad4-750d79d4a5be.png">

- 다음과 같이 올려진 이미지가 전부 Up 상태여야 정상인 상태이다.

- **지금까지의 폴더 구성**
  - aws ec2 우분투가 있고 그 안에 nginx 컨테이너랑 django 컨테이너를 생성
  - 이 2개를 같이 실행해주기 위해서 docker-compose를 사용
  - 그래서 여기에 80번 포트로 접속하게 되면 → docker nginx로 연결되어 있어서 nginx로 보내준다
  - 그럼 nginx 안의 docker가 django에 있는 uwsgi socket과 연결을 해줘서
  - django에서 앱이 실행되고 → 다시 nginx한테 응답을 주고 → nginx도 응답을 클라이언트한테 줘서 웹서버가 실행

- **여기까지 진행하고 ip주소로 입력하면 정상적으로 브라우저 화면이 뜨게 된다.**

```terminal
docker-compose up 
```

- 해당 명령어는 빌드를 안 해도 기존의 컨테이너로 구동시키는 명령어이다.




