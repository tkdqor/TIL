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




- 또다른 방법으로는, CSS를 활용해서 강제로 inline element인 img element를 block element로 변경시킬 수 있다. 
- display라는 property에다가 block이라고 지정하면 -> inline element가 block element가 된다. 그리고 margin에 auto를 줘서 가운데로 정렬하는 방식.
