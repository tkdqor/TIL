## Vs code로 환경 구축
**1) 터미널에서 cd Desktop으로 바탕화면에 가고 cd env로 가상환경 안쪽까지 들어가고 그 안에 우리가 만든 프로젝트 디렉터리가 있어야 한다.**
- VS Code가 가상환경과 장고 프로젝트를 정확히 인식하기 위해서는, 가상환경 내부에 장고 프로젝트가 위치해 있어야만 한다. 이 장고 프로젝트는 env 가상환경을 베이스로 해서 돌아가는 것이기 때문이다.

**2) env 가상환경에 들어간 상태에서 source bin/activate 입력해서 가상환경 활성화 확인.**
- 그리고나서 cd heestagram으로 프로젝트 내부까지 들어와 있어야 한다. 그리고 ls를 입력했을 때 manage.py가 있으면 된다.

```terminal
code .
```

- 그리고나서 이렇게 입력해주면, vs code를 현재 디렉터리에서 열어 주게 된다. 따라서 지금은 우리가 만든 heestagram을 베이스로 해서 vs code를 열겠다는 의미이다. 
- 그러면, vs code에서 봤을 때 프로젝트 루트(프로젝트의 근간이 되는 폴더를 루트 디렉터리)가 세팅되어 있다.


**3) 이 상태에서, manage.py를 클릭하기**
- 그러면 좌측 하단에 python 버전이 보이게 된다. 이걸 클릭하면, 현재 VS Code에서 Python 코드를 해석하기 위해 어떤 python를 쓰고 있는지가 화면 상단에 Current: 이렇게 나오게 된다. 이 python은 우리가 python 3.9버전을 설치했을 때 그 python을 기반으로 해서 VS Code가 Python코드를 해석하고 있다는 것이다.
- 여기에 있는 python 중, env라는 가상환경 내부에 있는 python을 사용해야 한다.
- 가상환경 내부에 있는 python를 클릭해서 연동시키자.

**4) 터미널 버튼을 클릭**
- 터미널 버튼을 클릭하면, 자동으로 source /Users/kimsangbaek/Desktop/likelion/env/bin/activate 이렇게 source 명령어가 자동으로 실행되어 가상환경을 activate 해주게 된다.


### 맥북 Big Sur의 경우 참고할 점 
- 맥북 Big Sur의 경우, env 가상환경에 들어간 상태에서 활성화를 하는 것이 아니라 env를 포함하는 디렉터리를 만들고 -> 해당 디렉터리에 들어간 상태에서
```terminal
source env/bin/activate
code .
```

- 이렇게 활성화를 해주고 env를 포함하는 디렉터리에서 code . 를 입력해야 env 내부에 있는 python를 선택할 수 있다.



## 컴퓨터 네트워크
- 컴퓨터 네트워크에 있는 수많은 컴퓨터 중 한 대가 있다. 인터넷에 접속한 모든 컴퓨터는 숫자로 구성된 IP Address가 부여된다. 인터넷이라는 네트워크에 접속한 컴퓨터를 다른 용어로 **HOST**라고 부른다.
- 이러한 숫자로된 IP Address를 외우고 다니기 어려우니까 -> 텍스트 기반의 도메인이라는 개념이 도입되었다.

- 우리가 웹 서비스 상에서 우리가 원하는 server에게 데이터를 요청할 때에는 -> 이 IP 주소에 해당하는 server를 먼저 찾아간 다음에, 데이터를 요청하게 된다.
  - 그런데, server 한 대가 있을 때, 그 server는 웹 서비스 이외에도 다양한 서비스를(FTP서비스, 메일 서비스 등)동작할 수 있기 때문에 IP 주소만 사용하게 되면, server 앞까지는 갈 수 있지만 그 server가 제공하는 다양한 서비스 중에서 내가 어떤 서비스와 통신할지 결정할 수 없다. 우리가 웹 페이지를 얻고 싶은건지, 메일을 보내고 싶은건지 알 수가 없다.

-> 그래서 IP 주소를 활용해서 접근한 server 컴퓨터 내부에서 어떤 서비스와 통신할지 지정할 때 사용하는 것이 바로 **PORT**라는 개념이다.
   - IP Address에 추가로, port number를 사용하게 되면(ex. 54.237.40.203:80) -> 해당 server내에 있는 어떤 서비스에게 데이터를 요청할지, 혹은 데이터를 전송할지를 결정할 수 있다.

-> 우리가 배우는 웹 서비스의 HTTP 프로토콜에서는 80 port를 사용한다.(FTP 서비스는 21번 / 이메일 서비스는 25번을 사용한다.)
   - 그래서 IP 주소 뒤에 port 번호까지 적어줘야 server 내부에 있는 우리가 원하는 서비스를 콕 집어서 접근할 수 있다.


### 우리가 사용한 도메인
- 그런데, 우리는 지금까지 그냥 www.naver.com 이렇게 도메인만 적었지 그 뒤에 port 번호를 적어본 적이 없다. 우리가 인터넷 브라우저에서 도메인을 입력하는 행위는 -> 일반적으로 웹 서비스를 이용하겠다는 의미가 된다. 따라서 port 번호를 별도로 적지 않았을 경우에는 '이 사람이 지금 웹 서비스를 이용하고자 하니까 80 port로 연결하면 되겠다' 라고 브라우저가 알아서 해석해서 내부적으로 80 포트를 붙여서 사용하게 된다.
  - **그래서 www.naver.com 이렇게 입력하면 -> 이 도메인에 해당하는 IP Address를 알아낸 다음에 -> 해당 IP주소에 :80 를 붙여서 통신을 하게된다.** 


## HOST에게는 본인 스스로를 가리키는 특별한 IP 주소가 있다.
- 127.0.0.1이라는 IP 주소는 본인 스스로를 가리키기 위해 예약되어있는 특별한 주소로 Loopback IP 라고 한다. 
- 그리고 이에 대한 별칭으로 localhost라는 이름이 부여되어 있다. 즉, 2개가 같은 의미이며 서버 내부에서 뭔가를 진행한다는 것이다.
  - 해당 IP가 텍스트로 보이는 게 서로 같더라도 전혀 다른 server를 가리키게 된다. ex) 사람을 생각해보면 '나'라는 단어를 의미하는 것과 같다.


### django 서버
- django 서버는 기본적으로 localhost의 8000번 포트에서 동작하게 된다. 그래서 127.0.0.1:8000 이라고 말하게 되면 해당 server의 IP인 54.237.40.203의 8000포트, 즉 54.237.40.203:8000을 의미하는 것과 같다.
- ex) 웹 브라우저를 통해서 클라이언트가 www.sangbaek.com:8000 이라는 주소로 데이터를 요청하면 -> 내부적으로 DNS에 의해서 www.sangbaek.com이라는 도메인이 server의 IP인 54.237.40.203으로 변환되고 8000번으로 데이터를 요청하겠다고 변환이 된다. -> 그래서 IP주소에 해당하는 django server가 요청을 받고 응답을 해주는 것이다.

* 다만, 우리가 일반적으로 웹 브라우저에 도메인만 입력하고, 그러면 80번 포트가 붙게 된다고 했는데 -> 만약 웹 서비스가 django로 만들어진 서비스라면 8000번 포트를 사용하고 있을텐데 어떻게 연결이 될까?
  - Nginx 라는 서버 앞단에서 request를 가장 먼저 처리하는 웹 서버가(비슷한 다른 소프트웨어로 apache가 있음) 기본 포트인 80로 들어와도 django로 만들어진 서비스를 볼 수 있게 해준다.
  - 웹 서비스 배포 과정 시 진행해야 할 필수사항이다. 80 포트로 접속하면 nginx 에서 서버 내부의 8000포트 앱서버로 요청을 보내주는 것이다.
  - 참고 블로그 : https://dailyheumsi.tistory.com/19


### django 용어 정리
- 장고 웹서비스를 시작하기 위해 처음 만드는 큰 틀을 프로젝트라고 한다.
- 그리고 웹서비스에 필요한 기능 단위로 쪼개서 개발을 진행, django에서는 이 기능 단위를 App이라고 한다. 잘 만든 App를 다른 프로젝트에서도 사용가능하다.


### django App 생성
```terminal
python manage.py startapp posts(앱이름설정)
```

- 이러면 프로젝트 디렉터리 내부에 posts라는 앱, 패키지가 생성된다.

- 그리고 프로젝트 디렉터리 내부 urls.py에서
```python
from django.contrib import admin
from django.urls import path
from posts import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('posts/', views.index),
]
```

- 이렇게 from posts import views를 추가하고 url도 views모듈의 index함수를 설정하자. 

- 이제 views.py에서 index함수를 정의하자.
```python
from django.http import HttpResponse
from django.shortcuts import render

# Create your views here.

def index(request):
    return HttpResponse('Hello world!')
```

- views.py에 작성하는 모든 함수들은 첫번째 파라미터로 request를 적어줘야 한다.
- index 함수의 return으로 HttpResponse 함수를 사용할 때는, from django.http import HttpResponse 이렇게 import를 해줘야 한다. 괄호안에 문자열을 입력해서 http 응답으로 해당 문자열을 return 해주는 함수를 사용하게 된다.

- 이렇게 작성해주고 서버를 다시 작동시키고 localhost:8000/posts로 들어가면 Hello world! 라는 문자가 화면에 보여지게 된다.
