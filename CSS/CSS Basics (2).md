## 이미지를 정렬하는 방법
```html
<div>
<img src="gattaca.png" alt="프로필 이미지" width="20%" height="200px">
</div>
```
```css
div {
  text-align: center;
}
```
- img element를 div element로 감싼 다음에 div element에 text-align를 center로 적용시키기.
- text align은 inline-level element를 정렬하는 속성이라고 보면 된다.

## CSS Box Model
- HTML의 모든 element는 기본적으로 box에 해당한다. * 를 CSS selector로 border값을 적용하면 모두 box라는 것을 확인할 수 있다. 
<img src="https://user-images.githubusercontent.com/95380638/149442614-23a81968-1a6a-40f0-8e04-214ddd908651.png" width="60%" height="60%">
- padding, border, margin 이 3가지는 -> top / right / bottom / left를 모두 따로 설정할 수 있고 한번에 설정할 수도 있다.

```css
p {
  /* top - right - bottom - left */
  padding: 20px 40px 20px 40px;
}
```
- 이 block box에는 가로인 width와 세로인 height를 content 기준으로 지정할 수 있다.  
  실제 웹 브라우저에는 content의 width와 height에 padding과 border값을 더한 수치로 보여지게 된다. 
- 만약, Border를 기준으로 width와 height를 지정해주고 싶다면 -> box-sizing이라는 property에 border-box라는 value를 지정해주면 된다.

```html
<img id="profile" src="gattaca.png" alt="프로필 이미지" width="20%" height="200px">
```
```css
img#profile {
  display: block;
  margin: 0px auto;
}
```
- 또다른 방법으로는, CSS를 활용해서 강제로 inline element인 img element를 block element로 변경시킬 수 있다. 
- display라는 property에다가 block이라고 지정하면 -> inline element가 block element가 된다. 그리고 margin 좌우에 auto를 줘서 가운데로 정렬하는 방식.
