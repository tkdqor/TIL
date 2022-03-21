## Deploy (3)
- nginx는 우리가 별도로 터미널에서 실행시키지 않아도 항상 돌아가는데 반해서 gunicorn은 
```terminal
gunicorn heestagram.wsgi --bind 0:8000
```

- 이렇게 터미널에서 실행시켜줘야 한다. 그리고 컨트롤 + C로 종료할 경우, gunicorn도 종료된다. 이렇게 종료되면 웹 페이지를 새로고침 했을 때, 502 Bad Gateway라는 에러 메시지가 뜨게 된다.
- **nginx는 항상 동작하고 있고 모든 request를 nginx가 먼저 받는데, nginx가 해당 request를 gunicorn한테 전달해줘야 하지만 -> gunicorn이 죽어있으니까, nginx와 gunicorn간의 커넥션이 없으니까 에러가 뜨는 것이다.**
  - 그래서 우리가 터미널을 끄더라도 nginx처럼 gunicorn이 항상 동작하게끔 만들어줘야 한다.

### Linux 서비스
- 이러한 문제를 해결하기 위해 Linux 서비스라는 것이 존재한다. 이 서비스는 프로세스를 대신 실행시켜주고 EC2 server 자체가 재부팅 되더라도 알아서 해당 프로세스를 재시작해준다.
- 새로운 Linux 서비스를 만드려면 서비스에 대한 정보를 담고 있는 서비스 파일을 만들면 되는데, django-samples로 들어와 있는 터미널 상태에서(ubuntu상태) 
```terminal
ls /etc/systemd/system/
```

- 이러한 경로 내부를 확인해보면 Ubuntu 시스템에 이미 등록되어 있는 다양한 서비스를 확인할 수 있다. 이제 우리는 /etc/systemd/system/이라는 디렉터리 내부에다가 우리가 추가하고자 하는 gunicorn과 관련된 서비스를 새롭게 만들어줄 것이다.

```terminal
sudo vim /etc/systemd/system/gunicorn.service
```

- 이렇게 서비스파일의 이름을 정해주자. vim이 실행되고 i를 눌러 insert 모드로 들어간 다음, 서비스 파일을 작성하면 되는데 이 파일 역시 공유된 github의 django-sample 프로젝트의 scripts 안을 보면 gunicorn.service라는 파일이 있다. 해당 파일의 내용을 복사한 다음, vim 화면에 붙여넣자.
  - 해당 코드는, gunicorn 서비스의 설명을 적어주었고 해당 서비스를 실행할 주체를 정의하고 해당 서비스는 WorkingDirectory, env안에 있는 django-sample이라는 디렉터리를 기준으로 실행되어야 하는데 해당 경로에서 실제로 서비스를 구동시키기 위해서 입력해야 하는 명령어는 ExecStart=...  다음과 같다고 얘기하고 있다.

- 다만, 우리는 gunicorn을 가상환경 내부에 설치했고 우리가 지금까지 사용할 때는 env 들어가서 activate한 다음 그 다음에 gunicorn를 사용했지만 -> 이 Linux 서비스 파일에서는 가상환경을 activate하고 이런게 어렵기 때문에 -> 정확하게 가상환경 내부에 설치되어있는 gunicorn를 활용해서 명령을 실행하게끔 지정했다. 우리가 만든 env 가상환경 안에 설치되어있는 gunicorn를 사용해줘 라고 지정해줬다.

- 내용을 다 작성하고 esc를 누르고 command 모드로 간 다음, :wq로 저장 및 종료를 해주고 

```terminal
sudo systemctl enable gunicorn.service
```

- systemctl은 system control의 약자이다. 우리가 추가해준 gunicorn.service라는 파일을 바탕으로 gunicorn 서비스를 enable, 만들어줘 라고 하는 것이다.
- 입력하면 정상적으로 gunicorn 서비스가 등록된 것을 확인할 수 있다. 이러한 상태에서

```terminal
sudo systemctl start gunicorn.service
```

- 위와 같은 명령어를 통해서 해당 서비스를 실행까지 시켜주면 된다. 여기까지 모두 했다면 nginx를 사용했던 것처럼 

```terminal
sudo service gunicorn status
```

- 해당 명령어를 입력하면 -> 현재 gunicorn 서비스가 동작하고 있는지 확인할 수 있다. q를 눌러서 다시 명령어를 입력할 수 있게 해준다.
  - 그리고 만약 django 소스코드가 변경이 되어서 gunicorn를 재시작해야 한다면 

```terminal
sudo service gunicorn restart
```

- 이런식으로 진행해주면 된다.
- 이제 우리가 터미널을 종료해도, 직접 gunicorn를 실행시켜주지 않아도 Linux 서비스의 형태로 Linux가 알아서 gunicorn를 실행하고 관리해준다. 이제는 웹페이지를 새로고침하면 정상적으로 뜬다.


6:10
