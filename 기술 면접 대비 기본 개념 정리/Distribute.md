# Distribute

📖 **Contents**

- Distribute
  - [django 배포 관련](#django-배포-관련)
  - [AWS EC2 ssh 연결하기](#aws-ec2-ssh-연결하기)
  - [uWSGI란](#uwsgi란)
  - [DNS, DNS 서버란](#dns-그리고-dns-서버란)
  - [Collectstatic 명령어](#collectstatic-명령어)
  - [CI 예시](#ci-예시)
  - [MSA란](#msa란)

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
- https://blog.naver.com/PostView.nhn?isHttpsRedirect=true&blogId=parkjy76&logNo=221075843238&parentCategoryNo=&categoryNo=86&viewDate=&isShowPopularPosts=false&from=postView

- **리눅스 백그라운드 관련 내용**
  - **리눅스에서 동작하는 프로세스는 백그라운드(background)/포그라운드(foreground) 중 하나로 동작한다. 우리가 일반적으로 실행하는 프로그램은 대부분 포그라운드로 실행시키는 것이고, & 키워드를 사용하면 특정 프로세스를 백그라운드로 동작하게 할 수 있다.**
    - 포그라운드의 경우에는, 프로세스가 실행되는 동안 명령 프롬프트 상에서 아무것도 할 수 없게 된다.
    - 명령어 뒤에 &를 붙여서 입력하면 프로세스가 백그라운드에서 알아서 돌아가고 명령 프롬프트를 계속 사용할 수 있게 된다.
    - 개념적으로 멀티태스킹이라고 생각하면 된다.
    - 그리고 kill 명령을 통해 백그라운드에서 동작하고 있는 프로그램을 종료시킬 수 있다. 
  - https://jhnyang.tistory.com/395
  - https://itholic.github.io/linux-jobs-bg-fg/
  - https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%B0%B1%EA%B7%B8%EB%9D%BC%EC%9A%B4%EB%93%9C_%EC%8B%A4%ED%96%89

* * *

## DNS 그리고 DNS 서버란
- **DNS는 일반적으로 도메인을 IP로 전환하여 IP 네트워크에 통신해서 목적지 IP를 찾아가는 과정을 의미한다. 즉, 도메인과 대응된 IP 주소를 알려준다.**

![image](https://user-images.githubusercontent.com/95380638/175050768-a9c285d0-ab52-4ec8-92a8-95cd4622b547.png)

- 일반적인 DNS의 작동방식은 위와 같다. 출처는 [다음](https://gentlysallim.com/dns%EB%9E%80-%EB%AD%90%EA%B3%A0-%EB%84%A4%EC%9E%84%EC%84%9C%EB%B2%84%EB%9E%80-%EB%AD%94%EC%A7%80-%EA%B0%9C%EB%85%90%EC%A0%95%EB%A6%AC/)과 같다.

- **웹사이트의 데이터가 저장되어 있는 호스팅 서버는 인터넷 회선이 연결된 컴퓨터/장치이고 IP 주소가 할당되어 있다.** 그래서 이 주소가 실제 웹사이트 주소라고 할 수 있다.(ex. AWS의 EC2 서버)
  - **DNS 서버(즉, 네임서버)는 이러한 IP 주소를 특정 도메인 주소와 같다는 기록을 저장해두고 인터넷 사용자들이 도메인 주소를 검색했을 때 => IP 주소로 연결되도록 해주는 것이다.**

<br>

- **DNS 서버의 기본적인 IP 연결 과정**
  - ex) 한 사용자가 브라우저에서 naver.com를 검색했다면, 먼저 DNS 서버로 도메인 주소가 전달이 된다. 그리고 DNS 서버 내부에서 도메인 주소를 토대로 "naver.com = 12.123.123.123" 이라는 항목을 찾아내고 다시 브라우저에게 12.123.123.123의 IP 주소를 갖고 있는 호스팅 서버(해당 웹사이트 데이터가 저장된곳)로 가라고 지시하는 것이다. 그러면 브라우저가 다시 해당 IP 주소로 접속해서 웹사이트가 보이게 된다.

<br>

- **DNS 서버 종류 구분**
  - **DNS 서버가 초고성능으로 세상에 단 하나만 있다면 위 내용 그대로 이해하면 되겠지만, 그렇게 간단하지않다. 세상에 도메인 수가 엄청 많기 때문에 DNS 서버 종류를 계층화해서 단계적으로 처리하게 된다.**
  - 도메인의 총 관리는 ICANN에서 하기 때문에, DNS 서버도 최상위 도메인에서 개인 도메인의 서브 도메인까지 도메인 이름의 분류와 마찬가지로 디렉토리/계층 형태로 구분된다. 여기서 ICANN은 모든 인터넷 도메인과 DNS 서비스를 관리하는 기구이며, 일반적으로 ICANN - 레지스트리(Registry) - 레지스트라(Registrar) - 레지스트란트(Registrant)의 구조, 계층 형태를 가지게 된다.
  - **Root DNS Server** : ICANN이 직접 관리하는 서버로, TLD DNS 서버 IP들을 저장해두고 안내하는 역할을 한다.
  - **TLD(최상위 도메인) DNS Server** : 도메인 등록 기관(Registry)이 관리하는 서버로, Authoritative DNS 서버 주소를 저장해두고 안내하는 역할을 한다. 어떤 도메인 묶음이 어떤 Authoritative DNS Server에 속하는지 아는 이유는 도메인 판매 업체(Registrar)의 DNS 설정이 변경되면 도메인 등록 기관(Registry)으로 전달이 되기 때문이다.
  - **Authoritative DNS Server** : 실제 개인 도메인과 IP 주소의 관계가 기록/저장/변경되는 서버이다. 그래서 권한의 의미인 Authoritative가 붙는 것이다. 일반적으로 도메인/호스팅 업체의 ‘네임서버’를 말하지만, 개인 DNS 서버 구축을 한 경우에도 여기에 해당한다.
  - **Recursive DNS Server** : 인터넷 사용자가 가장 먼저 접근하는 DNS 서버이다. 위 3개의 DNS 서버를 매번 거친다면 효율이 좋지 않기 때문에, 한 번 거친 후 얻은 데이터를 일정 기간(TTL/Time to Live) 동안 캐시라는 형태로 저장해 두는 서버이다. 직접 도메인과 IP 주소의 관계를 기록/저장/변경하지는 않고 캐시만을 보관하기 때문에, Authoritative와 비교되는 의미로 반복의 Recursive가 붙는다. 대표적인게 KT/LG/SK와 같은 ISP(통신사) DNS 서버가 있고, 브라우저 우회 용도로 많이 쓰는 구글 DNS, 클라우드플레어와 같은 Public DNS 서버가 있다.
  - **기본적으로 브라우저는 캐시가 저장된 Recursive 서버를 사용하고, 실제 네임서버를 설정하는 곳은 Authoritative 서버라는 점만 주의해서 이해하자. 나머지 두 서버는 컨트롤할 수 있는 영역도 아니고 언급되는 경우도 적으니 전반적인 이해를 위해서만 알고 있자.**

<br>

- **DNS 서버의 자세한 IP 연결 과정**
  - ex) 브라우저에서 naver.com을 검색하고 사용하고 있는 통신사인 KT DNS 서버에게 도메인 주소에 해당하는 IP 주소를 요청(브라우저 기본 DNS 설정이 통신사 DNS 서버이기 때문)
  - ISP 서버에선 캐시 데이터가 없다는 걸 확인하고 루트 DNS 서버에게 어디로 가야 하는지 요청(캐시가 있다면 바로 Authoritative 서버에게 요청)
  - 루트 서버는 TLD DNS 서버 주소만 관리하기 때문에, xxx.com 도메인을 보고는 COM 최상위 도메인을 관리하는 TLD DNS 서버 주소를 안내
  - ISP 서버는 COM 서버에게 어디로 가야 하는지 다시 요청
  - COM 서버는 가비아 DNS 서버에서 해당 도메인이 관리되고 있는 걸 확인하고 안내
  - ISP 서버는 가비아 서버에게 또 다시 요청
  - 가비아 서버는 “naver.com = 12.123.123.123”이라는 정보를 확인하고 이 IP를 알려준다. 동시에 ISP 서버는 해당 정보를 캐시로 기록
  - ISP 서버는 브라우저에게 12.123.123.123라는 IP 주소를 안내
  - 브라우저는 12.123.123.123라는 IP 주소를 갖고 있는 호스팅 서버에게 웹사이트를 출력하라고 요청
  - 이제 마지막으로 우리가 보는 웹사이트 화면이 출력

* * *

## Collectstatic 명령어
- https://crynut84.github.io/2016/11/14/django-static-file/


* * *

## CI 예시
- 터미널에 테스트 커맨드를 CI에 입력해두면 → 푸쉬를 바탕으로 디펜던시 설치 후 커맨드 자체를 입력하게 되어 test.py 실행시키는 명령어가 실행된다.
- Github 자체 로컬 공간에 환경을 구성해서 우리의 코드를 실행하게 된다.


* * *

## MSA란
- **기존에는 Monolithic Architecture의 구조로, 소프트웨어의 모든 구성요소가 한 프로젝트에 통합되어 있는 형태였다.** 
  - 웹 개발을 예로 들면 웹 프로그램을 개발하기 위해 모듈별로 개발을 하고, 개발이 완료된 웹 어플리케이션을 하나의 결과물로 패키징하여 배포되는 형태이다.
  - 하지만 일정 규모 이상의 서비스, 혹은 수백명 이상의 개발자가 투입되는 프로젝트에서 Monolithic Architecture는 한계를 보인다.
  - 또한, 최근 어플리케이션 개발 방법은 CI/CD를 통한 개발부터 배포까지 빠르게 반영하는 추세인데, 많은 사람이 하나의 시스템을 개발하여 배포하기 때문에 시간이 오래 걸린다.

### MSA 개념
- **MicroService Architecture의 줄임말을 의미.**
  - 마이크로서비스란 작고, 독립적으로 배포 가능한 각각의 기능을 수행하는 서비스로 구성된 프레임워크라고 할 수 있다.
  - 마이크로 서비스는 서비스의 end-point(접근점)을 API 형태로 외부에 노출하고, 실질적인 세부 사항은 모두 추상화한다. 내부의 구현 로직, 아키텍처와 프로그래밍 언어, 데이터베이스, 품질 유지 체계와 같은 기술적인 사항들은 서비스 API에 의해 철저하게 가려진다.

### MSA 장점
- 각각의 서비스는 모듈화가 되어있으며 이러한 모듈까리는 RPC 또는 message-driven API등을 이용하여 통신한다. 이러한 MSA는 각각 개별의 서비스 개발을 빠르게 하며, 유지보수도 쉽게할 수 있도록 한다. 
- 팀 단위로 적절한 수준에서 기술 스택을 다르게 가져갈 수 있다. 회사가 java의 spring 기반이라도 MSA를 적용하면 node.js로 블록체인 개발 모듈을 연동함에 무리가 없다. 
- 마이크로서비스는 서비스별로 독립적 배포가 가능하다. 따라서 지속적인 배포 CD도 모놀로식에 비해서 가볍게 할 수 있다.

### MSA 단점
- MSA는 모놀리식에 비해 상대적으로 많이 복잡하다. 서비스가 모두 분산되어 있기 때문에 개발자는 내부 시스템의 통신을 어떻게 가져가야 할지 정해야한다. 또한, 통신의 장애와 서버의 부하 등이 있을 경우 어떻게 transaction을 유지할지 결정하고 구현해야한다. 
- 통합 테스트가 어렵다. 개발 환경과 실제 운영환경을 동일하게 가져가는 것이 쉽지 않다. 

- [관련 블로그](https://wooaoe.tistory.com/57)








