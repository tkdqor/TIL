## JavaScript와 jQuery

### JavaScript
- 웹브라우저 내에서 주로 구동되던 프로그래밍 언어
- 구글이 크롬 브라우저에서 사용되는 V8엔진을 오픈소스로 공개
  - 엔진의 역할은, 자바스크립트를 실행하기 전에 컴파일한 후에 실행, 그래서 빠르게 동작
  - 이 V8엔진을 통해 nodejs 플랫폼이 개발됨


### nodejs
- 범용 JavaScript 소프트웨어 플랫폼
- 주로 웹서비스 개발에 많이 사용
- Electron을 통해 Desktop 애플리케이션 개발도 지원


### jQuery
- 웹프론트엔드 자바스크립트 계의 혁명이었다. 
- 2006년, jQuery 출시
  - 그 전에는 브라우저 별로 파편화가 무척 심했음. 반면에 jQuery는 단일 API로 Ajax요청과 DOM조작을 편리하게 해주었다.

- 하지만, 최근에는 jQuery를 안 쓰는 경우가 많다.
  - 무거워진 jQuery, 하지만 방대한 기존 jQuery 플러그인
  - 보다 근대화된 브라우저
  - 보다 발전된 Web API
  - 웹 페이지 개념에서는 먹히던 라이브러리 (웹 페이지의 시대 -> 웹 애플리케이션의 시대) / jQuery는 하나의 유틸리티 라이브러리이기 때문에 웹애플리케이션을 개발하기 어려움.

- 최근 트렌드로 봤을 때, 리액트가 가장 인기가 좋고 jQuery는 점차 줄어드는 중 / 하지만 django admin이 jQuery인 것처럼 어느정도 알고 있으면 좋다.

- 대부분의 jQuery 메소드의 대안을 최근 브라우저에서는 네이티브 구현으로 제공

- jQuery에서는 특정 DOM에 다이렉트로 접근해서 뭔가 변경하는 작업을 많이 했다면, React / Vue / Angular에서는 그러한 작업을 잘 하지 않는다.


### jQuery 예시
- django admin에서는 jQuery를 활용한다.

- 먼저 jQuery CDN버전 적용
```html
<head>
  ...
<!-- Jquery CDN -->
    <script src="https://code.jquery.com/jquery-2.2.4.js" integrity="sha256-iT6Q9iMJYuQiMWNd9lDyBUStIq/8PuOW33aOqmvFpqI=" crossorigin="anonymous"></script>
  ...
</head>
```

- 위의 코드처럼 import를 진행하게 되면 -> jQuery라는 이름의 객체가 선언이 되는데, 이 객체에 대한 별칭이 $로 정의를 한다.

```html
<script>
// 이후에 필요한 자바스크립트 코드 구현
$(function() {
    console.log("웹페이지 로딩 완료");
});
</script>
```

- 그래서 이걸 함수처럼해서 호출할 때, 익명함수를 넘기게 되면 페이지가 준비되었을 때 호출이 된다. 필요한 모든 리소스, css나 자바스크립트가 모두 로딩이 되고 나서 호출이 된다.
- **console.log는 개발자도구 console 창에다가 출력하겠다는 코드이다.**


### 브라우저의 개발자 도구 열기
- 프론트엔드 개발에서는 필히 브라우저의 "개발자 도구"를 열어줘야 한다.
- 브라우저 자바스크립트 수행 중에 발생한 오류를 "console 탭"에서 확인할 수 있기 때문.
