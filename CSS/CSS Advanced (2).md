## CSS Advanced (2)

## block element 배치하기

### 1. margin 값 주기
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
