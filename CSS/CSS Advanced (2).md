## CSS Advanced (2)

## block element 배치하기

```css
#box1 {
    background-color: red;
}

#box2 {
    background-color: green;
    margin-left: 400px;
}

#box3 {
    background-color: blue;
    margin-left: 800px;
}
```

<img src="https://user-images.githubusercontent.com/95380638/149685095-02d92394-4804-46e8-9013-6312ba13ef7c.png" width="70%" height="70%">

- margin 값을 특정해서 주게 되면 box를 가운데로, 오른쪽으로 옮길 수 있다. 하지만 이런 경우, 브라우저의 크기가 바뀔 때도 그대로 유지가 되기 때문에 가운데가 아닐 수 있다.
- 이런 경우, 웹 브라우저에게 margin 값을 알아서 계산해달라고 지정해줄 수 있다.

```css
#box1 {
    background-color: red;
    margin-right: auto;
}

#box2 {
    background-color: green;
    margin-left: auto;
    margin-right: auto;
}

#box3 {
    background-color: blue;
    margin-left: auto;
}
```
<img src="https://user-images.githubusercontent.com/95380638/149685427-91bd5164-6699-444a-93f3-43d65df517e7.png" width="70%" height="70%">

- 이렇게 margin 값에 auto를 설정해주면, 브라우저에게 알아서 값을 주라고 지정하게 된다.


## block element 가로 배치하기

- 기본적으로 한 줄씩 차지하고 있는, 세로로 나열된 block element들을 CSS Flexbox를 이용하여 가로로 배치해보기
- flexbox에서 여러 개의 block element들을 가로로 나열하기 위해서는, 먼저 가로로 나열하려고 하는 block element들을 상위에서 감싸는 -> 새로운 block element를 만들어야 한다.

```html
<div class="container">
     <div class="box">Box</div>
     <div class="box">Box</div>
     <div class="box">Box</div>
</div>
```

```css
.container {
    display: flex;
}
```
<img src="https://user-images.githubusercontent.com/95380638/149686436-e9756675-c4e1-4004-9fa4-20abbbe43a56.png" width="70%" height="70%">

- 그리고나서 위의 코드처럼, 여러 개의 block element를 담고 있는 이 div container에 -> display: flex를 적용해서 flexbox container로 바꿔주겠다라고 해주면 된다.
- 그러면 자식 element들도 적용되어 한 줄에 모든 block element들이 가로로 배치가 된다.

* * *

### justify-content로 가로 배치 설정하기
- justify-content라는 property를 사용해서 가로 배치를 다양하게 진행해보자.
- 주요 value로는 center, flex-start, flex-end, space-between, space-around가 있다.

1. flex-start
- 우리가 굳이 지정하지 않아도 왼쪽으로 치우친 형태를 의미.


2. flex-end
- div container로 감싼 내부 element들이 오른쪽으로 치우치게 된다.
```css
.container {
    display: flex;
    height: 800px;
    background-color: yellowgreen;
    justify-content: flex-end;
}
```
<img src="https://user-images.githubusercontent.com/95380638/149686896-36be457e-7a99-4872-8913-6ee4c295865a.png" width="70%" height="70%">


3. center
- div container로 감싼 내부 block element들이 가로 방향의 가운데로 위치하게 된다.
```css
.container {
    display: flex;
    height: 800px;
    background-color: yellowgreen;
    justify-content: center;
}
```
<img src="https://user-images.githubusercontent.com/95380638/149687114-886507ae-ea97-43b0-ab2a-5fb0d22c2754.png" width="70%" height="70%">

- box에 적용되어있는 margin 값을 없애주면 box들이 공백없이 한 줄로 붙게된다. 


4. space-around
- 정렬하려고 하는 block element들 좌우로 여백을 균일하게 맞춰주는 value이다.
```css
.container {
    ...
    justify-content: space-around;
}
```
<img src="https://user-images.githubusercontent.com/95380638/149687339-7408bea8-4cd6-4926-b845-51baa0c1b115.png" width="70%" height="70%">


5. space-between
- space-between은 시작과 끝에 있는 element들을 맨 끝으로 붙이고 나머지 부분에 대해서 여백을 균일하게 맞춰주는 value이다.
```css
.container {
    ...
    justify-content: space-between;
}
```
<img src="https://user-images.githubusercontent.com/95380638/149687633-e88a8321-cb6d-41c2-9c4a-11c837a7f3cf.png" width="70%" height="70%">

* * *

### align-items로 가로 배치된 element들을 세로 조정하기
- align-items 라는 property를 이용해서 가로 배치된 element들을 세로로 조정해보자.
- 주요 value로는 flex-start, center, flex-end가 있다.

1. flex-start
- 우리가 따로 지정해주지 않아도 적용이 되어 있었던 것으로, div container 기준으로 내부 element들이 맨 위쪽 상단에 위치하게 된다.


2. 





