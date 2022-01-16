# CSS Advanced (1)

## 레이아웃
- 웹 페이지에서 레이아웃의 배치란, 다양한 block element의 배치라고 볼 수 있다.
- HTML 특성 상, 부모 element가 다양한 자식 element를 포함하고 그 자식 element도 다른 자식 element를 포함하게 된다.


- 따라서, 어떠한 레이아웃을 구성할 때는 block element의 섹션을 분리해보면서 구성해보자.
  - 맨 처음에는 header와 본문을 구분해서 생각할 수 있다. 물론 그 header도 콘텐츠에 따라 나뉠 수 있다.
  - 본문의 경우는 일단 좌우 여백과 실제 콘텐츠가 있는 부분으로 나눠볼 수 있다. 콘텐츠도 각각의 영역에 따라 나뉠 수 있다. ex) 줄 단위, 사진 단위 등등...

=> 그래서 결국, 우리가 실제로 웹 페이지를 개발할 때 가장 많이 작업하게 되는 부분은 **큼지막한 block element를 작은 block element로 쪼개고 또 배치하는 작업이다.**
- 기본적으로 block element는 한 줄을 다 차지해서 세로로만 배치가 가능하나, CSS를 이용해서 block element를 가로로도 배치할 수 있게 된다. 

* * *
## CSS Flexbox
- 대부분의 브라우저에서 지원하고 현업에서 많이 쓰이는 방법 중 하나인 CSS Flexbox를 이용해서 block element를 배치해보자.
- 
