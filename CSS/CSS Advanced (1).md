# CSS Advanced (1)

## 레이아웃
- 웹 페이지에서 레이아웃의 배치란, 다양한 block element의 배치라고 볼 수 있다.
- HTML 특성 상, 부모 element가 다양한 자식 element를 포함하고 그 자식 element도 다른 자식 element를 포함하게 된다.


- 따라서, 어떠한 레이아웃을 구성할 때는 block element의 섹션을 분리해보면서 구성해보자.
  - 맨 처음에는 header와 본문을 구분해서 생각할 수 있다. 물론 그 header도 콘텐츠에 따라 나뉠 수 있다.
  - 본문의 경우는 일단 좌우 여백과 실제 콘텐츠가 있는 부분으로 나눠볼 수 있다. 콘텐츠도 각각의 영역에 따라 나뉠 수 있다. ex) 줄 단위, 사진 단위 등등...

=> 그래서 결국, 우리가 실제로 웹 페이지를 개발할 때 가장 많이 작업하게 되는 부분은 **큼지막한 block element를 작은 block element로 쪼개고 또 배치하는 작업이다.**
- 기본적으로 block element는 한 줄을 다 차지해서 세로로만 배치가 가능하나, CSS를 이용해서 block element를 가로로도 배치할 수 있게 된다. 

## 레이아웃 예제
```html
<body>
    <div class="box">Box 1</div>
    <div class="box">Box 2</div>
    <div class="box">Box 3</div>
</body>
```
```css
.box {
    width: 300px;
    height: 300px;
}
```
<img src="https://user-images.githubusercontent.com/95380638/149648508-3e795eb0-5c99-4678-85d7-2172d7bcd515.png" width="70%" height="70%">

- 이 경우에는 캡처와 같이 개발자도구로 box 1를 선택해보면, 파란색으로 너비 300px / 높이 300px 이렇게 지정된 것을 확인할 수 있다.  
- 그리고 content는 들어있지 않지만 block element의 특성 상, 차지하고 있는 영역이 개발자도구에서 주황색으로 표시되고 있다. 이는 개발자도구의 CSS box에서 표시되는 색깔과 같으니 참고하자.  


- **block element 배경 색깔**
```css
.box {
    width: 300px;
    height: 300px;
    background-color: grey;
}
```
<img src="https://user-images.githubusercontent.com/95380638/149648715-6e168f6b-f1fe-417d-a414-ba971e6498aa.png" width="70%" height="70%">

- background-color라는 property는 block element의 content에 배경 색깔을 입혀줄 수 있는 property이다.




* * *
## CSS Flexbox
- 대부분의 브라우저에서 지원하고 현업에서 많이 쓰이는 방법 중 하나인 CSS Flexbox를 이용해서 block element를 배치해보자.
- CSS Box model 내용에도 나와있지만, 모든 HTML element는 Box model, box의 형태로 구성되어 있고 특히 block element의 경우에는 너비와 높이를 추가로 지닐 수 있다고 했다.  
  즉, block element는 padding, border, margin 이외에 width, height까지 가질 수 있다는 것. 
