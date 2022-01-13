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

* * * 

## 목록을 구성하기 위한 element

```html
<ol>
    <li>11</li>
    <li>22</li>
    <li>33</li>
</ol>

<ul>
    <li>11</li>
    <li>22</li>
    <li>33</li>
</ul>
```
각각 ordered list, unordered list의 약자로 순서가 있는 목록 또는 순서가 없는 목록을 나타낼 수 있다.  
실제 목록에 추가할 아이템은 list item, li element를 이용해서 하나씩 추가해주면 된다.

## 이미지 삽입을 위한 element
```html
<img src="lion.png" alt="Lion 1">
```
이렇게 img element를 사용해주면 된다. 경로를 지정하기 위해 source의 약자인 src attribute를 사용한다. alt attribute는 alternative text의 약자로 이미지 대체 텍스트를 의미.  
또한, 다운로드에 실패할 경우 화면에 대체 텍스트가 표시된다.

## 링크를 위한 a element
```html
<a href="https://www.google.com">Google</a>
<a href="https://www.naver.com" target="_blank">Google</a>
```
a는 anchor 즉, 닻에 해당하는데 인터넷이라는 바다에서 정박할 다음 목적지를 가리킨다고 보면 된다. 하이퍼텍스트를 만들기 위해서는 이 a element를 사용하자.  
가장 중요한 것은, hyperlink Reference의 약자인 href attribute인데 이 attribute의 value가 곧 하이퍼링크를 눌렀을 때 이동하게 되는 링크의 주소가 된다.  
a element의 content는 웹 브라우저 상에서 우리 눈에 보여질 링크의 형태이다. 단순한 텍스트부터 이미지, 다른 html element까지 모두 링크로 만들 수 있다.  
target attribute를 언더바blank로 지정하면, 링크를 눌렀을 때 이동해야 할 페이지가 새로운 창 혹은 새로운 탭에 뜨게 된다.

## 컨텐츠를 묶여주는 용도인 div element / span element
- div와 span 모두 본인이 그 자체로 어떤 의미를 지닌다기보다는 다른 element나 콘텐츠를 필요에 따라 그룹으로 묶어주는 용도로 사용됨.
- CSS를 적용시킬 때 -> 이 div와 span에 의해 묶여져 있는 것을 바탕으로 원하는 스타일을 입힐 수 있게 된다.

### block-level element / Inline-level element
- HTML element를 특성에 따라 두 종류로 구분. 보통 block element / inline element라고 부른다.

**1. block element**
- 항상 새로운 줄을 차지하는데, 가로 폭은 부모 element의 가로 폭만큼을 전부 차지하게 됨. 세로 폭은 자신의 content 높이만큼만 차지.  
- 그래서 block element를 추가할 때마다, 줄바꿈이 되면서 다음줄에 레이아웃이 생성되고 블록이 차곡차곡 쌓이는 형태가 된다.
- ex) h1~h6 element, p element, ul/ol/li element

**2. inline element**
- 본인의 content 사이즈만큼만 공간을 차지하게 된다. 그래서 줄바꿈을 발생시키지 않고 다른 텍스트와 한 줄에 이어지게 된다. 
- ex) a element, img element

## div element
```html
<div>
    <h1>...</h1>
    <p>...</p>
</div>    
```
- div element는 document division의 약자로, 묶어줌과 동시에 새로운 블록을 형성하는 block element에 해당한다.
- 제목과 문단을 나눌 때 사용해보자.
- HTML5 이전에는 레이아웃 구성을 위해 정말 많은 div element를 사용하게 되었다. 그러나 너무 많아져서 각 div가 어떤 역할과 기능을 의미하는지 판단하기가 어려워졌다.  
  - 그래서 HTML5에서는 이러한 문제를 해결하기 위해 div element와 동일하지만 이름만으로도 기능과 역할을 알 수 있게끔 Semantic element라는 게 추가되었다. 



## span element
```html
<p>
    <span>Lorem ipsum</span> dollar sit met....
</p>
```
- span element는 묶음의 결과가 inline element이기 때문에 다른 내용들과 어우러져서 같은 줄에 위치하게 된다.
- 텍스트를 묶어주고 강조할 때 사용해보자.
