# HTML element

* * * 

## HTML element의 종류
```html
<h1></h1>
...
<h6></h6>
```
h1 element부터 h6 element는 HTML 문서에 배치할 수 있는 제목에 해당. h는 heading의 약자. 중요도에 따라서 font-size가 점점 작아진다.  
중요도에 따라서 h1부터 사용을 해야 한다.

```html
<p>...</p>
```
p는 paragrah의 약자로 HTML 내에서 관련있는 콘텐츠를 하나로 묶어서 문단을 구성할 때 주로 사용.  
p element뿐만 아니라 HTML에서는, 여러 번 띄어쓰거나 줄바꿈을 하더라도 공백은 하나만 있는 것으로 간주됨.

```html
&nbsp;
```
그래서 HTML에서 띄어쓰기를 강제하기 위해서는 다음과 같은 텍스트를 입력하면 되고

```html
<br>
```
줄바꿈을 강제하기 위해서는 br element를 추가해주면 된다.

```html
&lt;...&gt;
```
그리고 웹 페이지에 부등호 기호를 element를 생성하기 위한 용도가 아니라 화면에 출력한 용도로 사용하고 싶다면 위와 같은 텍스트 사이에 입력해줘야 한다.
- 다양한 특수기호는 https://dev.w3.org/html5/html-author/charref 해당 사이트를 참고해보자.

```html
<p>lorem</p> -> vs code 자동완성 선택
```
아무런 의미는 없지만 공간을 채워서 레이아웃에 연출하기 위한 용도로 사용되는 lorem ipsum을 사용해서 문단에 공간을 채울 수 있음.
