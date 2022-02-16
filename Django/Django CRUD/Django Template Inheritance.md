## Django Template Inheritance
- django template을 구성할 때, 똑같이 반복되는 코드들이 많을 수 있다.
  - 기본적으로 HTML 문서 자체를 구성하기 위한 각종 메타 정보들은 페이지가 바뀌어도 변함이 없다. 만약 이러한 부분이 2천만 줄이 넘는 코드로 구성되어있다면, 매번 작성하기 어려울 것이다.

- 이러한 문제를 해결하기 위해, Template Inheritance(상속) 라는 방법이 존재한다.
  - 여러 template에서 공통적으로 사용하는 코드를 추출해서 하나의 base template으로 만들고 -> 이 base template을 상속 받아서 base template과 차이가 나는 부분만 작성하게끔 하자.
  - 여러 template이 만약 공통적인 style을 사용하고 있고, 화면 상단에는 Navigation / 하단에는 Footer 이렇게 구성되어 있다면 안에 contents만 다를 뿐 전체적인 틀은 똑같이 유지가 된다.

- 그래서, 공통적인 레이아웃에 해당하는 부분을 뽑아서 base.html로 구성하고 
  - 웹 페이지별로 차이가 나야하는 부분을 Contents Block이라는 특별한 영역으로 지정해준다. 그리고 각 페이지에서는 이 Contents Block에 해당하는 부분을 채워주면 된다.
