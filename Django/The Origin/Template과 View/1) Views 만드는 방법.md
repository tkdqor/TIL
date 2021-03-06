## Views 만드는 2가지 방법


### View란
- 웹 요청을 수신하고 응답하는 파이썬 함수 또는 클래스
- 그래서 컨트롤러 및 비즈니스 로직을 처리하는 역할을 하게 된다.


### Views를 만드는 방법
1) **FBV : Function Based Views**
- 함수 기반 뷰로 구현이 간편하고 코드가 읽기 쉽고 직관적이다. 데코레이터의 간단한 사용법이 존재한다.
- 반면에 코드 확장과 재사용이 어렵다. 조건부 분기를 통한 HTTP 메서드 처리가 필요.
- 일회성 / 특수 목적을 갖고 있는 View에 적합


2) **CBV : Class Based Views**
- 클래스 기반 뷰로 코드 확장과 재사용이 용이하다. 다중 상속, Mixin이 가능. 내장 Class Based View를 사용할 수 있다.(ListView, CreateView, DetailView..)
- 반면에 읽기 어렵고 구조가 복잡해진다. 데코레이터 사용 시 함수를 재정의 해야 한다.
- 일반적인 생성, 조회, 수정, 삭제 등에 View에 적합
