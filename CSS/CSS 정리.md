## CSS 정리

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










* * *

- 부모 element에는 사이즈를 정하지 않은 상태에서 -> 자식 element에다가 height를 100vh를 설정해서 브라우저를 채우게 할 수도 있다.




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
