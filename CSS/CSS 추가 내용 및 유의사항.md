## CSS 추가 내용

(1) body element도 div element이다.
```html
<body id="resume_container">
...
</body>
```

```css
#resume_container {
    display: flex;
}
```
- 다음과 같이 body element에 id를 주고 가장 큰 div element로 설정할 수도 있다.


(2) width를 calc()를 사용해서 설정하는 방법
```html
<body id="resume_container">
    <div class="nav">asdf</div>
    <div class="content">qwer</div>
</body>
```

```css
.nav {
    width: 280px;
}

.content {
    width: calc(100% - 280px);
}
```

- 다음과 같이 width를 calc()를 사용하게 되면, 2개의 div element를 한 화면에 공백없이 채울 수 있다. 왼쪽 div가 280px의 너비를 가지고 오른쪽 div에서는 100%에서 280px를 제외한 너비로 설정할 수 있다.


(3) CSS Selector 설정 관련
```css
.resume_link a {
    ...
}
```

- 이렇게 css selector를 설정하면, 클래스가 resume_link인 element 내부에 있는 a element를 설정하는 것을 의미한다.


(4) 자식 element가 부모 element 너비와 높이를 넘어서는 레이아웃으로 구성될 경우에는, 부모 element에 overflow property 적용
```html
<div class="resume_sections">
    <div class="resume_section">section 1</div>
    <div class="resume_section">section 2</div>
    <div class="resume_section">section 3</div>
</div>    
```

```css
.resume_sections {
    oveflow: hidden;     /* 또는 scroll */ 
    ...
}
```

- 부모 element에 overflow: hidden이라는 값을 주게 되면, 부모 크기의 영역을 빠져나가는 부분이 가려져서 스크롤이 되지 않는다.
- 만약 overflow: scroll이라는 값을 주게 되면, 빠져나가는 영역은 가리고 스크롤의 형태로 내려서 볼 수 있게끔 해준다.


(5) 특정 텍스트에만 스타일링을 적용하기 위해서는, 
```html
<h1><span>My</span> resume</h1>
```

- 이렇게 span element를 사용해서 id나 class를 설정하고 스타일링을 적용할 수 있다.


(6) 줄과줄 사이의 여백을 주고싶을 때는 line-height 사용
```css
.class {
    line-height: 28px;
}
```

- line-height property을 설정하면 행간을 조절할 수 있다.


(7) 아이콘 관련해서는 fontawesome 사이트 활용해보기
- 아이콘들을 ul element 내부에 있는 li element로 설정하고, ul element의 CSS에서 font-size를 설정하면 아이콘 크기를 조절할 수 있다.


(8) 한 페이지에서 navbar로 이동시키기
```html
<div><a href="#ABOUT">ABOUT</div>

...
    
<div>
    <div id="ABOUT">ABOUT</div>
</div>
```

- 한 페이지내에서 이동시키기 위해서는 div element에 특정 id를 지정하고 a element의 href attribute에 #id이름을 지정해주면 된다.


(9) a element에 커서 닿을 때 기능 추가
```css
.section-icons a:hover {
    color: #4169E1;
    transition: 1s;
}
```

- section-icons라는 클래스 안에 있는 a element가 있다면, a:hover 이렇게 selector를 설정하고 커서를 댈 때 색깔이나 나타나는 속도를 transition으로 설정해줄 수 있다.



* * *

## CSS 적용 시 유의사항

1) 가장 먼저 CSS Reset 설정

2) 배치 및 정렬은 감싸는 부모 element로 display: flex; 적용.   
   부모도 안에 자식 element들이 여러개이든 아니든 상관없이 width와 height 크기 조절하기.

3) 내부적인 크기 조절은 해당 element로 진행하기. 그리고 margin이나 padding도 내부 element에 주면서 여백 조절.   
   반응형 웹사이트를 만드려면 부모 elenment 크기 조절 시 vw & vh 사용해서 브라우저 기준으로 설정하기. 
   그리고 자식 element도 크기 조절 시 %로 하기. (그러면 부모 element 기준으로 상대적인 크기가 됨)   
   또한, 부모 element든 자식 element든 class로 설정하거나 자식이 딱 하나이면 id로 설정하기.

4) 부모 element에 display:flex하고 -> 자식 element에다가도 display:flex해도 상관없음

5) 이미지의 경우, 사진을 감싸는 div element 하나 만들고 감싸는 div의 크기 및 overflow 적용   
   사진은 id를 지정해서 크기 조절하고 object-fit 적용

6) 글의 경우, 글들을 감싸는 부모 element와 안에 글들은 h1 / ul / h2 / p 이런식으로 구성해보기   
   ul 안에 li가 여러개이면 ul에다가 클래스 주고 display:flex하기   
   글에서도 섹션을 구분짓고 싶다면 div을 생성해서 구분

7) margin, padding은 부모가 아닌 자식 element에게 클래스 주고 설정해보기.

8) 부모 element에는 사이즈를 정하지 않은 상태에서 -> 자식 element에다가 height를 100vh를 설정해서 브라우저를 채우게 할 수도 있다.

9) 부모 element에는 설정하지 않고, 자식 element에만 display: flex;를 설정해서 레이아웃을 잡을수도 있다.
