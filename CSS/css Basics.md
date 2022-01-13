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
