## RDS


### SQLite
- SQLite는 장고의 default DBMS이다. 데이터베이스 전체를 파일 하나에다가 저장하기 때문에 파일을 통째로 복사하면 백업이 끝난다. db.splite3라는 파일이 SQLite3를 통해서 만들어진 데이터를 모두 담고 있는 파일 하나에 해당한다. 
- 표준 SQL를 모두 지원하고 파일 하나로 모두 처리하면서 별도의 프로그램이 필요없기 때문에 편하게 사용할 수 있다. 반면에 퍼포먼스가 좋지 않아 프로덕션용으로는 적합하지 않다.

- 그리고 서버 1대로 서비스를 운영하는 것은 불가능하다. 서비스의 규모가 커짐에 따라 여러개의 server를 운영하게 되는데 -> 각 server에는 django 프로그램이 돌아가고 있고, SQLite 파일도 같이 존재할 것이다. 즉, 별도의 DBMS를 여러개 사용하게 된다는 의미가 된다.
  - 따라서 서비스 이용자들은 여러 server에 걸쳐서 골고루 분산이 될텐데, 이러한 경우 server마다 저장되어있는 데이터가 모두 달라지게 된다.
  - 처음 로그인했을 때는 1번째 server에 연동되다가 새로고침 후 다른 server에 연동이 된다면 다른 데이터를 받게 된다. 

- **그래서, 프로덕션 환경에서는 SQLite가 아니라 PostgreSQL이나 MySQL과 같이 고성능의 DBMS를 사용하게 된다.** 이 DBMS를 장고가 돌아가고 있는 server에서 같이 운영하는 것이 아니라, 별도의 server인 별도의 컴퓨터로 분리해서 -> **django server 1대 / DBMS 전용 server 1대** 이렇게 server를 분리해서 사용한다.
  - **이렇게 구성하게 되면 -> django server가 여러 대로 늘어나게 되더라도 데이터를 다루고 있는 DBMS server는 django와 별도로 분리되어 있고 네트워크로만 통신하기 때문에 client와 연동된 django server가 바뀌었다고 해서, 저장되어있는 데이터가 바뀌는 일이 발생하지 않게 된다.**


### Amazon AWS로 MySQL DBMS 생성해보기
- django server와는 별개로, 별도의 server에 DBMS를 구축해 사용하는 방법은 크게 2가지로 나뉜다. 
  - Amazon의 AWS를 사용한다고 했을 때 EC2와 같은 가상 server 컴퓨터를 발급받아서 처음부터 끝까지 DBMS를 설치하고 세팅하고 시작 및 백업과 모니터링을 우리가 직접 다 하는 방법이 있다.
  - 또한, 이러한 절차를 AWS가 알아서 책임지고 해주는 Managed Services인 RDS를 사용해 DBMS를 구축하고 사용하는 방식이 있다. 우리는 이 2번째 방법을 이용해보자.

1) 먼저 AWS 사이트에 접속하자. 그리고 내 계정 -> AWS Management Console를 눌러서 루트 사용자로 로그인하기.
2) 로그인하면 AWS Management Console 화면이 나오고 여기서 다양한 서비스에 접근해서 사용할 수 있다. 여기 검색창에 RDS라고 입력하고 클릭. 그러면 Amazon RDS라는 페이지가 나온다.
3) 리소스 탭에 보면 발생한 인스턴스를 파악할 수 있다. 하단에 데이터베이스 생성이라는 주황색 버튼을 클릭하자. 그러면 데이터베이스를 생성할 수 있는 상세 페이지로 오게 된다. 여기서 우리가 원하는 DBMS의 종류를 선택하고 server의 스펙을 선택한 다음 계정 정보나 네트워크 정보, 보완 관련 정보를 설정해주고 생성하면 간단하게 DBMS를 구축할 수 있게 된다.
4) 표준생성은 그대로 두고, 엔진 옵션에서 MySQL를 선택해주기 / 여기서 템플릿은 한정된 시간 동안 무료로 사용할 수 있는 체험판인 프리 티어를 선택.
5) 그 다음 설정에서는, **DB 인스턴스 식별자가 있다. RDS를 통해서 DBMS 전용 server를 구축할 수 있는데 이러한 각각의 server를 우리는 인스턴스라고 부른다.** 즉, RDS 인스턴스가 엄청 많다면 각각의 인스턴스가 어떤 서비스와 관련된 인스턴스인지 구분할 방법이 있어야 하니까 이름을 붙여주는 것이다. 
6) 그리고 자격 증명 설정에 마스터 사용자 이름과 마스터 암호 그리고 암호 확인이 있다. 데이터베이스에는 데이터가 저장되고 이 데이터는 개인정보를 포함해서 민감한 정보들이 많이 있을 것이다. 우리의 데이터베이스가 전세계에 오픈되어 있어 누구나 접속할 수 있고 데이터를 조회할 수 있다면 안될 것이다. -> 그래서 본인만 접속할 수 있게끔 마스터 계정을 생성해주는 것이다. 이러한 계정 정보를 입력하고 기억하고 있자.
7) 그 다음 DB 인스턴스 크기는 데이터베이스 server 스펙을 정하는 것이다. CPU를 몇 개 짜리 사용할 것인지, 메모리를 몇 GB를 사용할 것인지 등등 지정할 수 있다. 하지만, 프리 티어를 사용할 경우에는 CPU 하나와 메모리 1GB짜리 컴퓨터인 t2 micro 타입의 컴퓨터를 사용해야만 한다.
8) 연결 탭으로 와서 퍼블릭 액세스 부분에 예를 선택. 그리고 VPC 보안 그룹은 새로 생성을 클릭하고 보안 그룹 이름은 mysql로 설정하고, 밑에 추가 연결 구성을 눌러서 데이터베이스 포트가 나오는데 MySQL의 경우 3306이라고 나와있다.
9) 그리고 추가 구성 탭을 클릭해서 펼쳐주자. 데이터베이스 옵션에서 초기 데이터베이스 이름을 꼭 지정해주자.
10) 마지막으로 월별 추정 요금 탭을 보면, 나열된 조건하에 무료로 사용할 수 있다고 나온다. 여기까지 확인했으면 데이터베이스 생성 버튼을 누르면 된다.

- 위의 과정을 거치면 RDS 인스턴스가 하나 생성되고 있는 것을 확인할 수 있다. 이 인스턴스를 삭제하고 싶을 경우에는 클릭하고 작업 -> 삭제를 눌러서 삭제를 해주면 된다. 해당 인스턴스의 이름을 클릭해보면, 해당 RDS 인스턴스의 상세 정보를 확인할 수 있다.

11) 해당 RDS 인스턴스 상세 정보 화면에서 -> 연결&보안 탭을 누르고 우측에 VPC 보안 그룹이라는 mysql 링크가 있다. 이걸 클릭하자. 그다음, 이름이 mysql이라고 적혀있는 보안그룹을 체크한 상태에서 하단에 인바운드 규칙을 누르면, 해당 server에 어떠한 port로, 어떠한 IP 대역으로 접근할 수 있는지가 작성되어있다.
12) 우측에 인바운드 규칙 편집 버튼을 눌러서, 소스를 사용자 지정에서 -> 2개 중 하나는 Anywhere-IPv4 그리고 Anywhere-IPv6으로 바꿔준다. 이러면 모든 IP 대역에서 MySQL server에 접속할 수 있게 된다.
    - 이렇게 설정하는 이유는, 지금은 우리가 고정된 IP를 사용하지 않고 강의를 듣는 환경에 따라 IP가 유동적으로 바뀔 것이기 때문에 이렇게 설정하는 것이다. **이 데이터베이스에 접속하는 주체가 한정되어 있다면, 그리고 IP가 고정되어 있다면 -> 해당 IP 혹은 해당 IP 대역만 등록해서 보안을 강화할 수 있다.** 마지막으로 규칙저장을 눌러서 인바운드 규칙을 저장하자.

13) 화면 상단에 다시 RDS라고 검색해서 Amazon RDS 페이지로 이동하면, 우리가 인스턴스를 하나 만들었기 때문에 DB인스턴스(1/40)이라고 뜨게 된다. 해당 링크를 클릭해서 인스턴스 목록으로 가면 우리가 만든 인스턴스를 확인할 수 있다. 인스턴스를 다시 클릭하고 연결&보안 탭을 누르면 **엔드포인트** 라는 것이 있는데, 이게 바로 host 주소를 의미한다. 이 host 주소가 내부적으로는 IP 주소로 변환이 된다. 그리고 포트는 3306을 사용하고 있는 것을 확인할 수 있다.
    - **그래서 이러한 host 정보 / 포트 정보 / 인스턴스를 만들어줄 때 작성했었던 마스터 계정 정보를 활용해서 RDS에 접속한 다음 MySQL를 이용할 수 있다.**

* * *

### Django 데이터베이스 설정 바꾸기
- 이제 MySQL를 사용하기 위해 django 내에서 데이터베이스 설정을 바꿔야 한다. 프로젝트 이름과 동일한 이름의 디렉터리 내부에 있는 settings.py에서 DATABASES라는 변수가 있다. 기본적으로 우리가 수정하지 않는 한, 데이터베이스 엔진으로 sqlite3를 사용하고 있는 것을 확인할 수 있다. 이 부분을 MySQL로 수정해주면 된다.
  - 먼저 **NAME**이라는 항목은 데이터베이스의 이름이 된다. 초기에 설정한 데이터베이스 이름을 적어주자.
  - 그리고 **USER**는 마스터 계정의 아이디를 의미한다. 즉, 초기에 설정한 마스터 사용자 이름을 적어주면 된다. **PASSWORD**는 마스터 계정의 패스워드를 적어주면 된다.
  - 마지막으로 **HOST와 PORT**는 RDS 인스턴스 상세 페이지에서 엔드포인트 및 포트에서 확인했던 내용을 적으면 된다. 엔드포인트를 복사해서 HOST에 붙여넣고 포트를 복사해서 PORT에 붙여넣으면 된다.

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'onandoff',
        'USER': 'onandoff',
        'PASSWORD': '패스워드 입력',
        'HOST': 'onandoff.ccd6tbburw5u.us-east-1.rds.amazonaws.com',
        'PORT': '3306',
    }
}
```

- 이제 server를 구동시켜서 사용하면 되는데, 사용하기전에 django에서 데이터베이스 엔진으로 MySQL를 사용하기 위해서는 운영체제별로 다른 라이브러리를 설치해줘야 한다.
  - MAC에서는 먼저 C언어를 컴파일하기 위한 C Compiler가 필요한데, Mac에서는 이를 위해 일반적으로 Xcode라는 프로그램을 사용한다. 그래서 터미널에
```terminal
xcode-select --install
```

- 이렇게 입력하면 C Compiler를 비롯해서 기본적인 프로그래밍을 위해 필요한 것들이 자동으로 설치가 된다. 

- 설치가 되었다면, 그 다음에는 MySQL를 설치해줘야 한다. Mac에서는 MySQL를 사용하기 위해서 Mac 컴퓨터 자체에, 로컬 환경 내부에 MySQL이 설치되어 있어야 한다. Mac 자체에 설치하는 방법은 다양하지만, 대부분의 Mac 유저가 알고 있고 사용하는 방법은 **Homebrew라는 패키지 관리자를 통해서 MySQL를 설치하는 것이다.**
  - 구글에 Homebrew라고 입력하고 Homebrew 홈페이지에 접속하자. 그리고 바로 화면에 보이는 코드를 복사해서 터미널에 붙여넣고 실행하면 된다.
```terminal
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

- 해당 명령어를 입력하면 password를 입력하라고 나오는데, 실제 Mac 계정의 비밀번호를 입력하자. 비밀번호를 입력하고 나서 Press RETURN to continue or any other key to abort -> 이렇게 뜨는데, 여기서는 enter를 입력하면 된다.

- 다운로드가 완료가 되서, 실제로 다운이 되었는지 확인하기 위해
```terminal
brew --version
```

- 다음과 같은 명령어를 입력했으나 zsh: command not found: brew -> 이렇게 없다고 나온다. 그리고 설치 과정에서 

```terminal
Warning: /opt/homebrew/bin is not in your PATH.
```

- 이와 같이 PATH에 등록되지 않았다고 나왔다. 그런데 친절하게 Next steps로 어떠한 명령어를 입력해야 하는지 알려준다.
```terminal
==> Next steps:
- Run these two commands in your terminal to add Homebrew to your PATH:
    echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/kimsangbaek/.zprofile
    eval "$(/opt/homebrew/bin/brew shellenv)"
- Run brew help to get started
- Further documentation:
    https://docs.brew.sh
```

- 그래서 터미널에 
```terminal
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/kimsangbaek/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"

brew --version

Homebrew 3.4.0
Homebrew/homebrew-core (git revision 4912b7df9fc; last commit 2022-03-07)
```

- 2줄의 명령어를 차례대로 1줄씩 입력하면, 이제 brew --version을 입력했을 때 설치된 Homebrew의 버전이 출력된다.



