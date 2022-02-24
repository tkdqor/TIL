## JavaScript
- JavaScript는 순수 html과 CSS로 구성된 static 웹 페이지, 정적 웹 페이지를 다이나믹하게 해주는 프로그래밍 언어이다.
- JavaScript의 대표적인 시각화 라이브러리인 D3.js의 홈페이지를 보면, 우리가 마우스를 움직임에 따라 화면이 계속 바뀌게 된다. HTML과 CSS를 활용해서 레이아웃을 구현하는 건 할 수 있지만, 마우스 커서가 움직이는
  것에 따라서 레이아웃을 동적으로 변경하는 건 아직 배우지 않았다.
  - 이렇게 커서의 위치를 실시간으로 파악해서 레이아웃을 원하는 형태로 늘렸다가 줄이면서 변경할 수 있다. 또한, 사용자가 어떠한 값을 선택하느냐에 따라서도 레이아웃 말고도 구체적인 수치, 모양도 변경할 수 있다.
  - 그리고 사용자가 웹 페이지의 특정 영역을 마우스로 클릭했을 때 어떻게 대응할 것이냐, 어떠한 액션을 취할지 이런 기능들을 Javascript로 기술할 수 있다.

- JavaScript는 브라우저 내부에서 동작하는 프로그래밍 언어로 그래서 python처럼 다양한 데이터를 저장하거나 조작하고 연산할 수 있다. 그리고 웹 페이지를 컨트롤할 수 있는 거의 모든 기능을 포함하고 있어서 거의 다 구현할 수 있다.

- 브라우저에서 개발자도구 - console Tab을 누르면 -> 이 부분에 자바스크립트 코드를 입력해서 해당 웹 페이지를 기준으로 실행시켜 볼 수 있다.

- **우리가 django를 베이스로 해서 웹 서비스를 개발할 때에는 한 장의 웹 페이지를 보기 위해서 하나의 HTTP Request가 필요했다. 그런데 JavaScript를 잘 활용할 경우에 사용자가 특정 액션을 취할 때마다 
  동적으로 API를 호출해서 필요한 데이터를 그 때 그 때 불러온 다음, 웹 페이지의 일부분을 교체하는 형태로 즉각적으로 반응하는 웹 서비스를 개발할 수도 있다.**
  - ex) 쿠팡의 웹페이지를 보면, 특정 상품을 판매하는 웹 페이지의 경우에는 상품의 정보나 판매자 정보, 리뷰 정보 등 다양한 정보를 보여줘야 하는데 이 모든 정보를 다 포함하는 아주 큰 HTML 파일을 서버에서 다 완성해서
    client한테 내려주게 되면 -> 사용자가 핸드폰으로 웹 사이트에 접속했을 때 느리다고 느낄 수 있다.
    - 그래서 쿠팡의 경우에는 제품 상세 페이지에 접속했을 때 반드시 봐야 하는 최소한의 정보들만을 가지고 HTML 페이지를 완성해서 server에서 client로 전달하고 / 사용자가 해당 웹 페이지를 이용하는 과정에서 
      스크롤을 내리다 보면 상품평 / 상품문의 등에 접근할 수 있다. 이와 같이 추가로 사용자가 봐야 하는 정보가 있다면 사용자가 스크롤을 어느 시점까지 내렸거나 상품평 Tab를 클릭했을 때 -> 그제서야 JavaScript로 
      server로부터 데이터를 가져와서 웹 페이지의 일부분을 교체해 버린다던가 새로운 HTML element들을 추가해주는 것이다.
      
- 이러한 구성 덕분에 사용자는 웹 사이트에 빠르게 접속할 수 있고 점진적으로 페이지가 완성되니까 사용자가 볼 수 있는 데이터는 많지만, 느리다고 생각하지 않게 되는 것이다.


### JavaScript 변수
- JavaScript에서도 변수를 var / let / const 이렇게 3가지로 만들 수 있다.
  - const는 변하지 않는 변수, 상수를 의미한다. 그래서 값을 재활용하기 위해 저장만하고 변경하지는 않는다.
```javascript
const name = 'sangbaek'
```

  - let은 값을 저장도 하고, 추후 변경하고자 할 때 사용한다.
```javascript
let age = 29
age = 30
```


### JavaScript BOM & DOM (Browser Object Model & Document Object Model)
- 참고 블로그 : https://goddino.tistory.com/71
- **JavaScript가 웹 생태계에서 강력한 힘을 가지는 이유는, 브라우저 자체와 HTML 문서를 자유롭게 컨트롤 할 수 있기 때문이다.**
  - 브라우저와 HTML 문서를 하나의 object로 표현해놨기 때문에 우리는 JavaScipt 코드로 해당 object에 접근하고 object를 조작할 수 있게 된다. 

- ex) Browser Object Model에 해당하는 location이라는 것을 활용하면 -> **location.reload()** 를 console에 입력해서 자동으로 현재 웹페이지의 주소를 새로고침할 수 있다.        
  또한, **location.href** 로 현재 웹 페이지의 url를 확인할 수도 있다. 그리고 **location.href = 'https://www.google.com'** 를 입력해서 바로 해당 주소로 이동할 수도 있다.

- ex) 또다른 Browser Object Model인 history를 통해서는 -> **history.back()** 를 입력하면 전에 봤던 페이지로 이동하는 뒤로가기 기능을 사용할 수 있다.


### Document Object Model
- 웹 페이지 화면에서 개발자도구를 열고 console를 클릭한 다음 **document**라고 입력하게 되면 웹 페이지 전체가 하이라이트 되는 것을 볼 수 있다. 이 document라는 변수가 html 문서 자체를 의미한다.
  - ex) **document.body.style.backgroundColor = '#000000'** 이렇게 입력하게 되면 -> 해당 문서내에 있는 body element에 접근해서 CSS style 중에 backgroundcolor를 #000000인 검은색으로 변경시킬 수 있다.
- 그리고 개발자도구에서 element Inspector를 이용해서 어떠한 HTML element가 사용되었는지, 어떠한 CSS Style이 입혀졌는지 확인할 수 있었는데 JavaScript에서도 가능하다.
  - ex) **document.querySelector('img[alt=Google]')** -> 이 querySelector 함수는 우리가 지정한 CSS Selector를 기반으로 해서 HTML element를 검색해준다. 여기서는 img element 중에서도 alt attribute가 Google이라고 작성되어 있는 HTML element를 찾아줘라는 것이다. 이렇게 입력하면 우리가 찾고자 했던 해당 img element를 검색해준다.
    - 그리고 더 나아가서 검색만 하는것이 아니라, JavaScript 코드로 접근하고 조작하는 것까지 가능하기 때문에 **document.querySelector('img[alt=Google]').srcset = '다른이미지주소'** -> 이렇게 입력해주면 구글의 로고를 다른 이미지로 대체할 수 있다.

- 이렇게 우리가 보고있는 웹 페이지에 접근해서 거의 모든 것을 JavaScript로 진행할 수 있다.


### Socket
- 관련 블로그 : https://helloworld-88.tistory.com/215
- 이렇게 우리가 원하는 시점에 웹 페이지를 다이나믹하게 바꿀 수 있다는 JavaScript의 특성을 최대한 활용해서 그 반응성을 극단으로 올리게 되면 https://socket.io/demos/chat 다음과 같은 실시간 채팅 서비스를 개발할 수도 있다. 우리가 채팅 메세지를 보내면 html 페이지를 바꿔서 또 보내고 새로운 li element를 추가하는 것이 가능해진다.
  - 물론, 이러한 채팅 서비스를 개발하려면 우리가 지금까지 배웠던 HTTP 방식에다가 Socket 이라는 특수한 방식을 추가로 사용해야만 한다.
  - 그래도 채팅 메세지라는 새로운 데이터를 전달받았을 때 해당 데이터를 활용해서 웹 페이지에 새로운 element를 추가해주는 것이 바로 JavaScript의 기능이다.

- **이처럼 우리가 원하는 시점에 원하는 형태로 웹 페이지를 변경하거나 새로운 html element를 추가해야 한다면 반드시 JavaScript를 활용해야 한다.**


14분!
