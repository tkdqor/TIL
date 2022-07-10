# Security

📖 **Contents**

- Security
  - [패스워드는 서버에 어떻게 보관될까](#패스워드는-서버에-어떻게-보관될까)
  - [세션 인증](#세션-인증)
  - [JWT 토큰이란](#jwt-토큰이란)
  - [OAuth란](#oauth란)

* * *

## 패스워드는 서버에 어떻게 보관될까
- **단방향 해시 함수를 사용하여 패스워드를 암호화해서 보관한다.**
- 단뱡향 해시 함수는 어떤 수학적 연산이나 알고리즘에 의해 원본 데이터를 매핑시켜 완전히 다른 암호화된 데이터로 변환시키는 것을 의미한다. 이 변환을 해시라고 하고, 해시에 의해 암호화된 데이터를 다이제스트(digest)라고 한다. 이렇게 DB에 저장된 다이제스트는 DB가 해킹당한다 하더라도 단방향으로 해싱된 문자이기 때문에 복호화를 할 수 없다.

- [관련 블로그](https://st-lab.tistory.com/100)


* * *

## 세션 인증
- **세션으로 권한 및 인가를 허용하는 과정은 다음과 같다.**
  - 사용자가 로그인에 성공하면 세션을 발행
  - 그 세션 id를 브라우저에 쿠키로 저장하고 서버 메모리 또는 데이터베이스에도 세션을 저장
  - 인가가 필요한 요청을 보낼 때 서버에 쿠키인 세션 id를 보냄 
  - 서버에서 메모리 또는 데이터베이스에 저장된 값과 쿠키인 세션 id와 비교해서 맞다면 권한 및 인가를 허용

* * *

## JWT 토큰이란
- **전반적인 개념**
  - 초기엔 하나의 토큰 access_token(또는 id_token)만 존재하여, 사용자 인증을 진행했다. 다만, access_token은 저장되는 값이 아니기에 유출되었을 때, 서버에서 이를 잘못된 사용자인지 판단할 수 없었다.
  - 그렇기에 Lifespan(토큰 생존주기) 를 짧게 설정하여 이를 최대한 방어하려고 했다. 그 결과, 유저가 로그인을 자주해야하는 상황이 발생했다.
  - **이를 해결하기 위해 OAuth 2.0 (최근 Overall 하게 사용하는 인증체계, 2012~) 부터 access_token / refresh_token 구조를 채택하게 되었다.**
  - access_token / refresh_token에서 access_token 은 (1시간 ~ 1일, 금융 등 중요 기관에선 30초) 등 짧은 Lifespan을 갖고, refresh_token 은 (2주, 1달, 영구) 등 긴 Lifespan을 가지게 된다. 이로인해 앞서 유저가 토큰 만료로 로그인해야하는 시점에 refresh_token으로 access_token을 재 발급 받게 된다. 여기서 물음이 드는것은, 그러면 refresh_token이 유출되면 똑같이 문제 아닌가라고 생각할 수 있다.
  - **aceess_token은 DB에 저장되지않고(프론트에는 저장되겠지만) 서버에서 토큰 자체를 해석해서 인증을 하는것과 달리, refresh_token은 토큰 자체에 유저정보도 있지만, DB(database, file, redis 등)에 저장되는 값이기 때문에 서버측의 의도로 만료시킬 수 있다. (기술적으로, refresh_token을 저장하지 않고 유저 로그와 refresh_token을 대조 하는 등 다른 방법도 있다)**
  - 그렇기에 특수한 상황(다른 디바이스 접속, 다른 아이피 접속, 다른 지역 접속 등)이 발생했을 때, 서버가 임의로 refresh_token를 만료시켜 로그인을 방지할 수 있다.
  - **또한, 중요한 점은 access_token은 우리가 개발한 API 서버(Resource Server)로 전달되겠지만, refresh_token은 우리 API 서버가 아닌 별도의 인증 서버에서 처리해주는 것이다.**
  - **인증서버(Authorization Server)란, 소규모 프로젝트(과제같은)에선 하나의 서버에서 관리하겠지만, 대규모 서비스의 경우 별도로 인증서버를 관리하는 것이 일반적이다. (Authorization 서버와 Resource 서버를 분리하는게 더 일반적인 아키텍쳐)**
  - 또한 우리가 사용하는 SNS 로그인, Cognito(AWS)로그인 등은 인증서버가 해당 회사 서버이다.(AWS, Facebook, Kakaotalk). 
  - 해당 인증서버에서는 refresh_token으로 요청한다고 바로 access_token을 주는게 아닌, 여러방법으로 보안을 유지한다.
  - **보안 유지 방법으로는, 앞서 말한 상황(다른 디바이스 접속, 다른 아이피 접속, 다른 지역 접속 등)이 발생 시 refresh_token을 만료 시키고 재로그인 요구할 수 있다. 그리고 추가적으로 사용자인증(휴대폰 문자 인증 등)을 요구할 수도 있다.**
  - 즉, access_token을 탈취하기엔 생명주기가 짧아 이를 방지하고, 공격자가 refresh_token을 탈취하더라도, Authorization 서버의 인증, 보안 절차로 인해 공격이 어렵게 된다.
  - **JWT Logout 관련, JWT 토큰은 서버에서 제거할 수 없기에 다음과 같은 절차로 logout을 구현한다.**
  - **첫번째로 브라우저에서 토큰을 삭제(프론트에서 삭제)하는 방법이다. 가장 일반적이다. -> 로그아웃 시, 브라우저에서 토큰을 삭제한다. 또는 서버가 내려주는 비어있는 값(access_token:"") 으로 업데이트 한다.**
  - **두번째로 DB BlackList를 관리하는 것이다. 로그아웃한 access_token을 blacklist라는 DB를 만들어 저장하여, 판단한다. Redis 또는 Database table을 이용해 구현(이경우에도 만료시간되면 DB에서 삭제) 아니면, 로그아웃 시 바로 우리의 DB에 저장한 refresh_token를 삭제시킬 수도 있다.**
  - **세번째로 토큰 만료기간을 짧게 초, 분단위로 설정하여 토큰이 빠르게 만료되도록 설정하는 방법이다.** -> 유저 로그아웃 즉시 만료되지는 않지만, 추후 해당 토큰 유출을 통한 공격을 방지할 수 있다. -> 이 경우, 유저가 로그인 말고 다른 API 요청 시, 자동으로 access_token을 새로 발급하거나 -> API 요청 후, 토큰 만료로 인한 에러 발생 시, 클라이언트가 자동으로 refresh_token으로 재발급 해서 새로운 access_token으로 다시 API 요청하게 된다.

- **토큰 방식**
  - **회원가입 시 유저에 매칭되는 토큰을 생성하여 저장한다. 로그인 요청이 들어오면 해당 토큰을 응답으로 보내주고, 클라이언트는 이 토큰을 잘 가지고 있다가 요청을 보낼 때 헤더에 토큰을 넣어 보낸다. 서버는 요청으로 들어온 토큰이 있는지 확인하여 인증하게 된다. 토큰 자체에 사용자에 대한 정보가 있어서 서버는 이 토큰만을 가지고 어떤 유저인지 구분해낼 수 있다.**
  - **토큰은 기본적으로 암호화방식을 채택한다. 이 암호화에 필요한 키는 바로 우리가 settings.py에서 본 SECRET_KEY이다.** 따라서 이 키가 노출되지 않는다면 토큰 자체로는 어떤 정보도 얻어낼 수 없다. 보안을 강화하기 위해 토큰에 대해 유효기간을 설정해줄 수 있다.

- **JWT(JSON Web Token)이란,** 
  - 예를 들어, eyJhbGciOiJIUzI1NiIsIn 이러한 형식이다. 사용자가 로그인을 하면 서버에서 토큰을 발행해주는 것으로, 이 토큰을 서버가 기억하고 있지 않는다.
    - 인가가 필요한 요청을 보낼 때 사용자는 해당 토큰을 보내야 한다.
  - 이 토큰은 암호화된 3가지 데이터를 이어붙인 형태(aaa.bbb.ccc)로 구성되어 있다. 
  - 첫번째로는 헤더 : 세번째 서명 값을 만드는데 사용하는 알고리즘과 type이 들어간다. ex) HS256 / JWT
  - 두번째로는 페이로드 : 토큰이 갖는 데이터를 의미.
  - 세번째로는 서명 : 1번 헤더에 정의된 알고리즘을 통해 암호화한 비밀 값으로 서버만 알고 있음

- **JWT 단점**
  - 세션처럼 모든 사용자들의 상태를 기억하고 있지 않기 때문에 기억하는 대상들의 상태를 항상 제어할 수 없다.
  - 이미 발행한 토큰이 해커한테 넘어가도 토큰을 무효화할 방법이 없다.

- **해결책으로 로그인 시 accessToken / refreshToken 이렇게 2개의 토큰을 발행해준다.** 
  - accessToken은 매번 인가받을 때 사용하는 토큰(수명이 짧다)
  - refreshToken은 accessToken의 수명이 다했을 때 accessToken을 재발행 받기 위한 토큰(보통 2주 정도 유지)
  - 누군가를 로그아웃 시키려면 refeshToken을 db에서 지워버리면 되는데 그래도 accessToken의 수명 동안은 바로 차단할 방법은 없음

- [관련 블로그](https://velog.io/@syoung125/JWT-%ED%86%A0%ED%81%B0%EC%9D%B4%EB%9E%80)


* * * 

## OAuth란
- **현재는 Oauth 2.0 표준으로 주로 구현되고 사용된다.**
- 사용자가 어떤 애플리케이션을 이용할 때, 비밀번호를 입력하지 않고 OAuth를 제공하는 애플리케이션에 계정 정보를 공유하여 접근 권한을 부여할 수 있는 수단
- OAuth가 사용되기 전에는 각 애플리케이션이나 웹 사이트마다 개별적인 인증 방식으로 아이디와 비밀번호를 사용하여 로그인 => OAuth는 제각각인 인증방식을 표준화한 것

<br>

- **관련 용어**
  - 구글이나 페이스북을 우리가 제어하고자 하는 자원을 갖고있는 서버라는 뜻에서 **Resource Server** 라고 지칭, 즉, 데이터를 가지고 있는 서버 / **Authorization Server**도 있다. 인증과 관련된 처리를 전담하는 서버
  - 일반 사용자 및 유저는 이 자원의 소유자이기 때문에, **Resource Owner** 라고 한다.
  - 우리의 웹서비스를 이 resource server에 접속해서 정보를 가져가는 **Client** 라고 한다.

<br>

### 전체적인 흐름
- 사용자가 우리 서비스에 접속해서 글을 작성하거나 글을 읽는다면, 우리의 서비스가 사용자를 대신해서 서비스 제공자(구글 등)의 달력에 날짜를 기록하는 등을 해줄 수 있다. 이러기 위해서 우리가 사용자로부터 사용자가 사용하고 있는 서비스 제공자에게 접근할 수 있도록 허가를 받아야 한다. 서비스 제공자의 사용자 id, 비밀번호가 있을 텐데, 그것들을 우리가 사용자로부터 전달받아서 우리가 사용자의 id와 비밀번호를 기억하고 있다가, 우리가 실제로 서비스 제공자쪽으로 접속할 때 그 id와 비밀번호를 이용하면 될 것이다. **근데, 사용자 입장에서는 위험하다. 자신의 구글, 페이스북의 id와 비밀번호를 처음 보는 웹서비스쪽에 맞겨야 되는 것이기 때문이다. 그래서 사용자는 우리의 서비스를 믿을 수 없다. 또, 구글이나 페이스북 입장에서도 자신들의 사용자의 id와 비밀번호를 신뢰할 수 없는 제 3자가 가지고 있다는 건 매우 불만족스러운 상황이다.** 바로 이러한 상황에서 우리가 OAuth를 이용하면 훨씬 더 안전하게 우리가 만든 서비스를 서비스 제공자와 사용할 수 있게 된다.  
  - OAuth에서는 대신에 사용자의 요청에 의해서 구글이나 페이스북이 id, 비밀번호 대신에 **accessToken**이라는 것을, 일종의 비밀번호를 발급해준다. 이 accessToken은 구글이나 페이스북의 id와 비밀번호가 아니라는 장점이 있다. 그리고 구글이나 페이스북이 갖고 있는 모든 기능이 아니라, 그 중에 우리의 서비스가 필요한 필수적인 기능만 부분적으로 허용하는 비밀번호이다. 그래서 우리의 서비스가 이 accessToken을 OAuth를 통해서 획득한 다음에, 우리가 이 accessToken를 통해서 구글이나 페이스북 서비스에 접근해서, 데이터를 가져오고 수정하고 생성하고 삭제하는 작업을 할 수 있게 되는 것이다. 

<br>

### OAuth 등록절차 - Client가 Resource Server에 등록하는 과정
- **클라이언트가 리소스 서버를 이용하기 위해서는, 리소스 서버의 승인을 사전에 받아놔야 한다. 이걸 이제 “등록”이라고 한다.** 
- 이 등록을 하기 위해서는 **Client ID와 Client Secret 그리고 Authorized redirect URIs** 이 3가지 요소를 리소스 서버로부터 공통적으로 받는다. 
  - 여기서 client ID라고 하는 값은 우리가 만들고 있는 애플리케이션을 식별하는 식별자, ID라고 보면 된다. Client Secret이라고 하는 값은 위의 ID에 대한 비밀번호이다. 그래서 client ID는 외부에 노출될 수 있지만, Client Secret은 절대로 외부에 노출되면 안 된다. 
  - 그 다음으로 Authorized(권한이 부여된) redirect URIs라고 하는 것은, 리소스 서버가 우리에게 권한을 부여하는 과정에서 우리한테 **Authorized CODE**라는 값을 전달해 줄 것이다. 그 때, ‘이 주소로 전달해주세요!’ 라고 알려주는 것이다.

- ex) **Google Cloud Platform**에 들어가서 새로운 프로젝트를 생성하고 Credentials이라는 제어할 자격을 얻기 위한 과정을 진행한다. OAuth client ID를 클릭하고 Configure consent screen 이라는, 사용자가 인증하는 과정에서 보여지는 화면을 설정한다. 그리고 OAuth Client ID를 생성하게 된다. 또한, Authorized redirect URIs를 적어주면 Client ID와 Client Secret이 생성된다.

<br>

### OAuth Resource Owner의 승인 - Resource Owner의 인증과정
- 우리가 위의 과정처럼 등록을 하게 되면, Resource Server와 Client는 양쪽 다 예를 들어서 Client ID : 1 / Client Secret : 2 / Authorized redirect URIs : https://~~~~ 이러한 정보를 resource Server가 알게 되고 / 동시에 Client도 그 정보를 알 수 있게 된다. 
- 그리고 Client는 이  Authorized redirect URIs에 해당되는 페이지를 구현하고 준비를 해놓고 있어야 한다. 
- 그리고 Resource Server가 갖고 있는 기능이 예를 들어 A,B,C,D 4개라면 ==> Client가 Resource Server의 모든 기능이 다 필요한 게 아니라, B와 C에 해당되는 2개의 기능만이 필요하다면, 모든 기능에 대해서 인증을 받는 것이 아니라 최소한의 기능에 대해서만 인증받는 것이 훨씬 더 좋다.
- **이제 Resource Owner, 즉 유저는 우리의 애플리케이션에 접속을 할 것이다.**
  - **근데 이 접속을 하는 과정에서 우리의 웹 서비스가 Resource Server를 사용해야 되는 상황이 있을 것이다. 예를 들면 페이스북에다가 글을 게시한다든지, 구글 캘린더에다가 날짜를 기록한다든지 이러한 작업을 해야된다면, 우리 웹 서비스는 Resource Owner에게 “소셜 로그인 화면”을 보여주게 된다. “페이스북으로 로그인하기” “구글로 로그인하기” 등 아니면 “귀하께서는 이 기능을 사용하기 위해 이 인증을 거쳐야 합니다..” 라는 메세지를 띄우게 된다. 뭐가 되었든 사용자가 동의를 해야지만 그 다음 과정으로 진행할 수 있다.** 
  - “Login with Facebook” 이렇게 생긴 버튼들을 클릭한다는 것은, 그 동의를 하겠다는 의미이다. 이 버튼을 클릭했을 때, https://resource.server./?client_id=1&scope=B,C&redirect_uri=https://client/callback 이런식으로 **우리의 client_id값과 우리가 사용하고자 하는 기능, 그리고 redirect URIs이 담기게끔 GET방식으로 링크를 만들어 요청할 수 있게끔 해준다.**

- 그래서 resource Owner가 해당 링크(해당 주소로)를 클릭해서 Resource Server에 접속하게 되면, resource Server에 현재 Owner가 로그인이 되어있는지 아닌지를 봐서, 로그인이 안되어 있으면 
로그인을 하라는 화면을 보여주게 된다. 
- **resource owner가 로그인을 해서 성공했다면 resource server는 그 때서야 client_id 값과 같은 client_id값이 있는지 확인하게 된다. 그리고 resource server가 갖고 있는 client_id인 1은 redirect_url이 ...인데, 지금 접속을 시도하는 버튼 클릭의 요청에 Redirect_url 값과 같은지 다른지를 확인해서 다르면 여기서 작업을 종료시킨다.**
- **만약 같다면 이 resource Owner에게 이 scope=B,C에 해당하는 권한을 Client에게 부여할 것인지를 확인하는 메세지를 전송하게 된다.** 

<img width="145" alt="image" src="https://user-images.githubusercontent.com/95380638/174522501-7b49e284-76bf-4d86-82c0-e6e19fdcbc76.png">

- 여기서 이러이러한 권한을 어떤 Client가 지금 요청하고 있는데, 허용할것이냐라고 물어보고 있는 것이다. 여기서 허용버튼을 누르면 / 허용했다는 정보가 Resource Server로 전송이 된다. 
  - **그러면 resource Server는 user id : 1 / scope : b,c 라는 정보를 수집하게 된다. User id 1번이 resource owner의 아이디 번호라고 해보자. 즉, user id 1번은 scope b와 c에 대한 작업을 허용하는 것에 동의했다는 것이다.**

- **여기까지가 Resource Owner로부터 Client가 resource Server에 접속하는 것에 대한 동의를 구하는 과정이라고 할 수 있다.**

<br>

### OAuth Client의 정보 전달 및 Resource Server의 확인과정
- 이전까지 Resource Owner가 이런 작업을 하는 것을 허용합니다라고 승인버튼을 눌렀을 때, 그 정보가 resource Server에 가서 user id : 1 / scope : b,c 이러한 정보를 받게 된다. 그 다음에는 Resource Server가 승인을 해줘야 한다.
- **이 때, resource Server는 임시비밀번호인 authorization Code를 이용한다.**
  - **Resource Owner가 승인 버튼을 눌렀을 때 => Resource Server는 이 authorization Code를 Resource Owner에게 전송한다.**
  - Ex) Location : https://client/callback?code=3 이런식으로 전달하게 되는데, 앞에 Location이 붙어있는 것은 응답할 때 header라는 값으로 Location을 주면, 이걸 redirection 하라는 의미가 된다. **Resource Server가 Resource Owner의 웹브라우저한테 https://client/callback?code=3 이라는 주소로 이동하세요 라고 명령한 것이다. 여기서 ?code=3이라고 하는 값이 임시비밀번호 authorization Code가 3이라는 걸 의미한다.**
  - Resource Owner의 웹브라우저는 Location Header 값에 의해서 Resource owner의 사용자가 인식조차 하지 못하게 https://client/callback?code=3이라는 주소로 이동하게 된다. 그럼 code=3이라는 값에 의해서 client는 authorization Code : 3이라는 데이터를 알게 된다.

- **이제 client가 resource server에게 authorization code = 3이라고 하는 정보를 전송해주는 것이다. Client는 Resource Owner를 통하지 않고 Resource Server에게 직접 접속을 하게 된다. https://resource.server/token?grant_type=authorization_code&code=3&redirect_uri=https://client/callback&client_id=1&client_secret=2 이런식으로 접속하게 된다.**
  - grant_type=authorization_code는 우리가 지금 authorization code를 통해서 3자간의 인증을 하고 있다는 의미이다.
  - code=3은 이렇게 authorization code = 3이라는 값을 전송한다는 것이고
  - redirect_uri=https://client/callback 이렇게 redirect_url 값도 전송한다.
  - 그리고 client_id=1&client_secret=2 이렇게 client_id 값과 client_secret 값도 전송한다.
  - **즉, authorization code와 client_secret이라고 하는 이 2개의 비밀정보를 결합해서 Resource Server에게 전송하게 되는 것이다.(즉, 클라이언트의 서버사이드에서 요청)**

- 이제 Resource Server는 authorization code가 3번인 것을 확인하고서 -> 자기가 갖고 있는 authorization code 3번에 해당되는 정보와 일치하는지 확인하게 된다.
  - **그리고 resource Server는 이 Client가 전송한 code=3과 client_id=1과 client_secret=2과 redirect_url의 값이 완전히 일치하는지를 확인하고 이 정보들이 모두 일치한다면 => 그 때, 다음 단계로 진행하게 된다.**

<br>

### OAuth Resource Server가 Client에게 AccessToken를 발급
- 지금까지 Client가 Resource Owner를 통해서 authorization code 값을 받았다. 그 다음에 Client는 Resource Server에게 직접 관련정보(authorization code/Client_id/Client_secret/redirect_url 등)들을 전송했다. 그 중에서도 가장 중요한 건, Client_secret이라고 하는, 외부에 절대로 노출되서는 안되는 정보를 직접 Resoruce Server에게 전송한 것이다. authorization code값과 함께 말이다.
- **이제 Resource server는 이미 authorization code 값을 통해서 Client를 인증했기 때문에 authorization code 값을 이제 지워버린다. Client 서버쪽에서도 지운다.**
- **그 다음에 이제 Resource Server는 AccessToken를 Client에게 발급해서 응답해준다. ex) AccessToken=4 이런식으로 말이다.**
  - 그러면 Client는 accessToken이 4라는 정보를 내부적으로 저장하게 된다.
  - **그리고 이 accessToken은 무엇을 보장하냐면, 해당 Client가 4라고 하는 accessToken으로 접근을 하게 되면 Resource Server는 accessToken : 4를 보고 ‘아 이 4는 user_id 1번에 해당하는 사용자, 즉 resource Owner의 유효한 기능인 b와 c에 대해서 권한이 열려있는 accessKey이니까 ==> 기능 b와c에 대해서, user_id가 1번에 해당되는 resource Owner의 정보에 대해서 accessToken = 4를 가진 사람, 즉 Client에게 허용을 해야겠다’ 라고 생각하고 동작하게 된다.**

<br>

### OAuth Client의 API 호출 진행
- **이제 이 accessToken를 활용해서 Client가 resource Server를 핸들링할 수 있게 된다.** 그런데 핸들링 및 조작하기 위해서는, Resource Server가 Client들에게 ‘우리를 사용하려면 이렇게 이렇게 하면 우리를 사용할 수 있어!’ 라고 알려주는 방식대로 해야 한다. 
- **바로 그 방식을 우리는 'API’라고 부른다.(Application Programming Interface)**
  - 우리 Client가 Resource server를 호출할 것인데, 이 때 Resource Server를 호출하는 그 접점에 있는 일종의 조작장치들을 API라고 부른다.

- **ex) 우리가 구글 캘린더를 resource Server로 해서 제어하고 싶다면, 구글에 google calendar api라고 검색해보자.** 뜨는 사이트를 클릭하고 “참조”를 클릭하면 여러가지 API들이 나온다. 지금 우리가 필요한 건, 구글 캘린더에 있는 캘린더 리스트 정보들을 가져오고 싶은 것이다. 검색결과 중, 관련 주소를 복사해서 GET 방식으로 보낼 때 필요한 뒷부분 주소를 붙여주고 브라우저에 입력해 보면, 해당 주소가 바로 구글 캘린더의 리스트를 보여주는 API가 되는 것이다.
- **그러면, 브라우저 화면에 JSON 형태로 데이터들이 나온다.. 지금은 로그인이 안되어있다고 뜬다. 즉, 인증이 필요한 API라는 것을 알 수 있다. 즉, OAuth / accessToken를 통해서 여기있는 데이터를 가져올 수 있게 된다.**
  - **Ex) 구글의 경우, accessToken를 쿼리 파라미터로 보내거나 / Authorization: Bearer라고 하는 것을 HTTP header로 전송하는 방법이 있다.** 여기서는 전자의 방법으로, API 주소 뒤에다가 GET방식으로 ?하고 access_token=ya29PbkdfjMFLsdf32dkjtsl 이런식으로 해주면 된다. 
  - 해당 주소로 브라우저에 입력하면 —> 이제는 JSON 형태의 구글 캘린더 데이터를 받을 수 있게 된다.
  - 후자의 방법은 curl이라는 프로그램을 이용해서 사용할 수 있다. Ex) curl -H 라고 하게 되면, -H는 옵션으로 H 뒤에 따라오는 “Authorization: Bearer <access_token>” 이라는 정보는 Header 값이 되는 것이다. 즉, Authorization이라는 Header의 값으로 Bearer라고 하는 OAuth를 위해서 고안된 인증 방법을 앞에다 놓고 그 뒤에 access_token를 적어주면 된다. 그리고 그 뒤에다가 우리가 접속하고자 하는 API 주소를 적어주면 된다. 이렇게 접속해도 마찬가지로 JSON 형태의 데이터를 받을 수 있고 더 안전하게 통신할 수 있다.

<br>

### Refresh Token이란
- **Access Token은 일반적으로 1,2시간 길게는 60일, 90일 등 보통 수명이 있다. 그 수명이 끝나면 -> 우리가 그 API에 접속했을 때 API가 더이상 데이터를 주지 않는다.**
- 그러면 우리가 이 accessToken를 다시 발급 받아야 하는데, 그 때 마다 사용자한테 위의 과정을 다시 거치게 하는 것이 힘들다.
  - **이런 경우에 손쉽게 우리가 새로운 AccessToken를 발급받을 수 있는 방법이 바로 refresh Token이다.**

- 구글 검색 창에 “OAuth 2.0 rfc” 라고 검색해보자. rfc는 인터넷과 관련된 여러가지 기술들의 표준안이라고 할 수 있다. RFC 6749를 클릭해보면 ==> “The OAuth 2.0 Authorization Framework” 라는 제목의 OAuth에 대한 설명이 들어가 있는 표준문서를 찾을 수 있다.
  - 해당 문서에서 목차를 보면, Refresh Token이 있다. 그리고 문자만으로 그림을 그려놓았다.
  - [여기에 보면](https://datatracker.ietf.org/doc/html/rfc6749#section-1.5) client가 있고 Authorization Server와 Resource Server가 있다.
  - 지금까지의 설명에서는 Resource Server와 Authorization Server를 그냥 하나로 퉁 쳤었다. 여기서는 구분하고 있다.

- 해당 그림을 분석해보면, 
  - A -> 먼저 client가 Authorization Grant라고 해서 권한을 획득하는 과정을 요청한다.
  - B -> **그리고 여러여러 과정을 거쳐서 access Token를 Authorization Server가 발급하게 된다. 근데 access Token를 발급할 때, 보통 함께 Refresh Token도 발급하는 경우가 많다.
그러면 Client는 이 2개의 Token를 모두 저장해놓고 있게된다.**
  - C -> API를 호출할 때는 Access Token를 제출하는 걸 통해서 
  - D -> Resource server에 있는 Resource를 가져오게 된다. (Protected Resrouce는 보호되고 있는 자원을 의미)
  - E -> 계속 이 토큰을 사용하다가 어느날, 
  - F -> 갑자기 Invalid Token Error가 뜨게 된다.(유효하지 않은 토큰 에러) 즉, Access Token의 수명이 끝났다는 것을 의미한다.
  - G -> **그럼 이제 우리의 Client는 우리가 보관하고 있었던 Refresh Token를 Authorization Server에게 전달**하면서 
  - H -> Access Token를 다시 발급받게 된다. 이 때, refresh Token도 새롭게 발급해서 계속 갱신되는 경우가 있고 / refresh token은 갱신되지 않고 access Token만 갱신하는 경우가 있다.

- **ex) 구글에서 access Token를 refreshing 하는 방법**
```terminal
POST / oauth2/v4/token HTTP/ 1.1
Host : www.googleapis.com
Content-Type : application/x-www-form-urlencoded
```

- 이렇게 나와있는데, www.googleapis.com/ oauth2/v4/token 이라는 path로 위의 Content Type은 —> form에서 POST 방식으로 전송하라는 의미이다.
- 그리고 이 때, client_id와 client_secret를 전송하고 / 보관하고 있었던 refresh_token 값도 전송한다. 그리고 grant_type이라고 하는 값을 전송하면 ==> 구글에서는 JSON 포맷으로 데이터를 리턴해준다. 여기에 새롭게 발급된 access Token값과 그 토큰이 얼마동안 유효한지에 대한 정보인 expires_in 값을 준다.

<br>

### OAuth 마무리
- OAuth라는 인증 시스템은 인증 과정에 참여하고 있는 3자 —> Client / Resource Owner / Resource Server가 한 자리에 모일 수 없는 상황에서 어떻게 하면 서로를 신뢰할 수 있을까 라고하는 고민에서 출발한 기술이다.
- OAuth의 가장 매력적인 부분은, 바로 Client의 입장에서 제3자인 resource server를 통해서 Resrouce Owner의 신원을 인증할 수 있다는 점이다.
- OAuth를 이용하는 궁극의 목적은, API를 제어하는 것이다.










