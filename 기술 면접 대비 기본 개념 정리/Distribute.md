# Distribute

📖 **Contents**

- Distribute
  - [django 배포 관련](#django-배포-관련)
  - [AWS EC2 ssh 연결하기](#aws-ec2-ssh-연결하기)
  - [uWSGI란](#uwsgi란)

* * *

## django 배포 관련
- https://dailyheumsi.tistory.com/19


* * *

## AWS EC2 ssh 연결하기
- AWS EC2 화면에서 접속하고자 하는 EC2를 체크하고 "연결" 버튼 누르기
- 그리고 터미널에서 pem키가 있는 디렉터리로 이동 (ex. cd Downloads)

```terminal
chmod 400 impact-redis.pem
```

- 다음과 같은 명령어 입력. **chmod는 순서대로 나/그룹/전체에 대한 권한을 의미한다. 지금은 나에게만 읽기 권한을 주는 것이다.**

```terminal
ssh -i "impact-redis.pem" ubuntu@ec2-13-209-43-88.ap-northeast-2.compute.amazonaws.com
```

- 그 다음에는 위와 같은 명령어를 입력해서 해당 EC2에 ssh로 접속하기


* * *

## uWSGI란
- https://brownbears.tistory.com/16
- https://gangpro.github.io/til/2020/02/20/til-til-djangoAWS04/
- https://www.tuwlab.com/ece/26607
- https://twpower.github.io/43-run-uwsgi-by-using-ini-file

- **리눅스 백그라운드 관련 내용**
  - **리눅스에서 동작하는 프로세스는 백그라운드(background)/포그라운드(foreground) 중 하나로 동작한다. 우리가 일반적으로 실행하는 프로그램은 대부분 포그라운드로 실행시키는 것이고, & 키워드를 사용하면 특정 프로세스를 백그라운드로 동작하게 할 수 있다.**
    - 포그라운드의 경우에는, 프로세스가 실행되는 동안 명령 프롬프트 상에서 아무것도 할 수 없게 된다.
    - 명령어 뒤에 &를 붙여서 입력하면 프로세스가 백그라운드에서 알아서 돌아가고 명령 프롬프트를 계속 사용할 수 있게 된다.
    - 개념적으로 멀티태스킹이라고 생각하면 된다.
    - 그리고 kill 명령을 통해 백그라운드에서 동작하고 있는 프로그램을 종료시킬 수 있다. 
  - https://jhnyang.tistory.com/395
  - https://itholic.github.io/linux-jobs-bg-fg/
  - https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%B0%B1%EA%B7%B8%EB%9D%BC%EC%9A%B4%EB%93%9C_%EC%8B%A4%ED%96%89







