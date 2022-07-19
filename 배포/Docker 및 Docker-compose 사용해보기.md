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
git remote origin 레포지토리 주소
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






