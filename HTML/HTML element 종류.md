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

