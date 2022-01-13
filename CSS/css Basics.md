# CSS
- CSS란, Cascading Style Sheet의 약자로 웹 페이지의 레이아웃을 구성하고 필요에 따라 다양한 스타일을 입히는 용도로 사용된다. 

## CSS Ruleset
```css
h1 {
  font-family: "Archivo Black";
}
```
CSS에서 선택하고자 하는, 원하는 element를 어떤 식으로 바꾸면 되는지 그 규칙을 의미.
- 먼저, 스타일을 바꾸고 싶은 HTML element를 selector로 설정.
- 그 다음에는, 중괄호안에 적용하려고 하는 스타일에 대해서 규칙들을 선언하기. 이를 declaration이라고 한다.
  - 각 선언들은 property: value;의 형태로 구성된다.
- 해당 ruleset를 외부 스타일 시트 / 내부 스타일 시트 / HTML tag에 스타일을 직접 지정하는 3가지 방식으로 적용해줄 수 있다.

## External CSS
- 별도의 독립적인 CSS파일을 먼저 만들고 HTML파일과 분리해서 적용하는 방식. 가장 추천되는 방식.

## Internal CSS
- HTML 파일의 head element 내부에 style element를 활용해서 css규칙을 작성하는 방식.

## Inline style
- 스타일을 적용하고자 하는 HTML start tag내부에다가 style attribute를 활용해 스타일을 적용하는 방법.

* * *
## CSS 실습
- 외부의 css 파일을 HTML파일내에서 사용할 수 있게끔 연결을 시켜주자.
```html
<head>
...
<link rel="stylesheet" href="style.css">
</head>
```
다음과 같이 head element 안쪽에 link element를 추가해주면 된다. href attribute에 연결하고자 하는 css 파일의 경로를 적어야 한다.  
ref attribute는 relationship의 약자로 이 HTML문서와 하이퍼링크로 연결하는 대상, 즉 여기서는 style.css라는 대상과의 관계를 나타낸다.

## CSS Ruleset 작성
- Ruleset에서 가장 먼저 적어줘야 하는 것은 바로 selector. 가장 쉬운 방법은 HTML의 tag 이름을 그대로 적는 것이다. 
```css
h1 {
    font-size: 50px;
    text-align: center;
    font-family: 'Nanum Myeongjo', serif;
}
```
다음과 같이 h1를 선택하면, HTML파일 내에서 h1이 사용된 모든 곳에 스타일이 적용된다.
- font-size : 글씨 크기를 변경하는 property. 대부분의 웹 브라우저에서 제목이 아닌 일반 텍스트의 폰트 사이즈는 16px이라고 한다. 
- text-align : 글씨를 정렬하기 위해 사용하는 property.
- font-family : 글씨체의 경우, 브라우저에 내장된 글씨체 중 하나를 사용하거나 우리가 원하는 글씨체를 서버에 직접 업로드해서 사용할 수 있음.  
  그런데, 웹상에서 제공하는 폰트를 사용하게 되면 따로 설치할 필요없이 편리하게 적용할 수 있다.
  - 대표적으로 **구글 웹 폰트**가 있다. https://fonts.google.com/ 해당 사이트에 들어가서 마음에 드는 폰트를 클릭하고 -> select this style 클릭해서 사용해보자.
  - 웹에서 사용하는 기본 글씨체는 serif - 글씨 끝을 살짝 멋을 냄 / sans-serif - 멋을 내지 않은 글씨체 / monospace - 자간이 일정한 글씨체 / cursive - 펜글씨같은 글씨체 / fantasy - 주로 제목용으로 사용하는 글씨체
  - 글씨체를 지정할 때는 사용하고자 하는 폰트만 적어주면 되나, 일반적으로는 예시처럼 콤마를 사용해서 여러 개를 지정하게 된다.  
    웹 브라우저는 이 font-family의 property를 앞에서부터 순차적으로 사용하게 되는데 그 과정에서 해당 폰트가 사용 불가능한 상황을 대비해서 해당 폰트를 대신할 폰트를 지정하는 것이다.  


## Parent/Children element
 ```css
body {
    font-family: 'Nanum Myeongjo', serif;
}
```
다음과 같이 body element를 설정하게 되면, body element 안쪽에 있는 모든 요소들에 글씨체가 바뀌게 된다.

- HTML에서 각 element는 content로 다른 element를 가질 수가 있는데, 이것을 상대적으로 표현하기 위해서 부모/자식 element라고 얘기한다.
- 즉, 특정 element를 기준으로 감싸고 있는 element를 부모 element, 감싸져 있는 element를 자식 element라고 한다.
=> CSS는 이 부모 element에 스타일을 주었을 때, 이 스타일이 자식 element에 전파될 수 있는데 이러한 현상을 **Style Inheritance**라고 한다.  
   다만, 어떤 property는 상속이 되기도 하고 안되는 경우도 있어서 이러한 부분이 CSS를 공부하는 데 있어 어려움이 된다.

## 글씨 색깔
```css
p {
    text-align: center;
    color: ;
}
```
- color : 글씨 색깔을 바꿔주는 property. 보통 코드 에디터에 있는 이름이 있는 색보다는, 디자이너가 요구하는 독특한 색상을 입혀야 하는 경우가 더 많다.  
          컴퓨터에서 색은 빛의 삼원색인 Red, Green, Blue 즉 RGB를 조합해서 만드는데 이 RGB를 표시하는 방법이 여러가지가 있다.  
          보통 Hexadecimal RGB 코드, 헥사코드를 많이 사용하지만 RGB 코드를 이용하기도 한다. 두 방법 모두 RGB 각각에 0~255까지 숫자를 부여해서 색을 표현한다.
          
## CSS Ruleset 작성 - 2
- CSS selector 설정 시, 보통 현업에서는 HTML tag 이름만 지정해서 선택하지는 않는다. 먼저 selector 선택 기준 3가지를 알아보자.
1. Type selector
- HTML element의 이름을 바탕으로 지정하는 방식.

2. Class selector
- class라는 attribute를 가진 모든 element를 선택하는 방식.

3. ID selector
- id라는 attribute를 가진 element 딱 하나만 선택하는 방식. 오직 하나만 있다는 것을 생각하자.

```html
<p id="main-article" class="article">
...
</p>

<p class="article">
...
</p>
```
- id와 class attribute는 모든 HTML element에서 자유롭게 사용할 수 있다. id와 class attribute를 사용하는 이유는 해당 element를 CSS에서 원하는 형태로 지정할 수 있게끔 하기 위함이다.
- 위의 



## 단위
* 절대 길이 단위 = 다른 요소에 의해서 길이가 변하지 않고 항상 동일한 크기로 간주되는 단위. 대표적으로 픽셀(px)이 가장 많이 사용됨.
* 상대 길이 단위 = 특정 요소를 기준으로 상대적인 크기를 계산해서 사용하는 단위. 웹 페이지 전반에 걸쳐 사용되고 있는 사이즈들을 모두 한번에 조절할 수 있음. 대표적으로 rem이라는 단위가 자주 사용됨.
- 참고할만한 페이지 : https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units  

