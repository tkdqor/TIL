## Django와 DRF 차이
- 우리가 예전에 만들었던 계산기를 DRF로 만들어보기
- **우선 API를 테스트 할 때는 임의의 클라이언트를 대체해줄 수 있는 프로그램을 사용하기도 한다. 그래서 이번에는 "postman"이라는 프로그램을 사용해보자. 우리가 백엔드 개발을 하기 때문에 우리가 클라이언트를
  만들 수 없다. 그래서 클라이언트가 요청을 날리는 것 처럼 해주는 것이다.** 우리가 API 서버를 만들게 되면 클라이언트 플랫폼 의존성이 낮다. 웹앱이나 안드로이드, iOS 기타 디바이스가 요청을 날려도 결국
  서버에서는 특정한 경우를 제외하고 그냥 똑같은 요청으로 받아드리기 때문에 이 postman으로 날려도 동일한 환경으로 우리가 확인을 할 수 있다. 
- https://www.postman.com/ 해당 사이트에 들어가서 OS에 맞게 설치해주기. 


### Postman
- 상단의 Workspaces를 클릭하고 Create workspace를 클릭한다. 그리고 ProjectLion이라는 이름으로 새롭게 생성해준다. 
- 그리고 상단 + 버튼을 클릭하고 url를 새롭게 생성해보자.
  - 우리가 이전에는 http://localhost:8000/ 이렇게 입력하면 브라우저로 DRF 화면을 확인할 수 있었다. 그래서 postman에서도 http://localhost:8000/ 이 URL를 그대로 입력하고 GET 요청으로 
    Send를 클릭하면

<img width="915" alt="image" src="https://user-images.githubusercontent.com/95380638/167800264-54730b33-a7a8-4853-bf26-a6ddeb4dbe2a.png">

<img width="1054" alt="image" src="https://user-images.githubusercontent.com/95380638/167800471-50282056-322d-4d9c-ac3e-95a6bc7fe89a.png">


- 이렇게 DRF 내용이 뜨게 된다. http://localhost:8000/posts/ 라고 요청하면 POST모델 데이터도 뜨게 된다. 사실 우리가 지금까지 확인했던 브라우저 화면은, DRF에서 GUI적으로 보기 편하게 제공을
  해주는 것이다.
- 그리고 postman에서 위의 url 그대로 POST 방식으로 보낼 수도 있다. 근데 추가로 데이터를 입력해야 한다. Body라는 부분을 클릭해서 x-www-form-urlencoded를 클릭하고 넣어주면 된다. 여기에 우리가
  넣어야 할 key를 추가하고 값들을 넣어주면 된다.

<img width="866" alt="image" src="https://user-images.githubusercontent.com/95380638/167801624-5febe2c9-9c6e-476b-a2f0-7efade0cde4c.png">

- **그러면 새롭게 데이터가 생성된다. 응답값, status code는 201번으로 나왔다. 그리고 속도도 표시해준다.**
- 이렇게 postman이 클라이언트 역할을 대신 해줄 수 있다. 

- **우리가 나중에 백엔드 개발을 하게 되었을 때, 클라이언트 또는 백엔드 둘 중 한군데에서 문제가 되어 기능이 정상적으로 수행되지 않는 경우가 있다. 그래서 우리가 API 서버를 만지다가 오류가 났을 때는 ->
  postman으로 직접 클라이언트 역할을 해서 API 요청을 해보고 제대로 된건지 확인을 하고 제대로 되었다고 하면, 클라이언트 쪽에서 문제가 있다고 판단할 수 있다. 근데 만약 postman으로 요청을 했을 때 
  서버쪽 응답이 이상하다면 서버쪽에 문제가 있는 것이기 때문에 내가 직접적으로 대응해줘야 한다.** 그래서 postman을 이슈 발생 시 판단용으로 사용할 수 있다. 

- 그리고 postman의 경우에는 Collections를 만들어 놓고 서로 관련있는 url별로 구분지어서 확인할 수 있다. 

<img width="761" alt="image" src="https://user-images.githubusercontent.com/95380638/167804675-9d06f3e1-39c8-476e-8832-08c21183ed57.png">

<img width="724" alt="image" src="https://user-images.githubusercontent.com/95380638/167805181-51fe7b76-6b27-4768-8eb8-de85cf186f1e.png">



  
