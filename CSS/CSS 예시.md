## Instagram 프로필 따라해보기

### 프로필 사진과 계정 정보를 담고 있는 container 구성
```html
<body>
    <div class="container">

    </div>
</body>
```

```css
.container {
    width: 940px;
    height: 200px;
    background-color: #fafafa;
    border: 1px solid #dbdbdb;
    border-radius: 10px;
    margin: 20px auto;
}
```

<img src="https://user-images.githubusercontent.com/95380638/149880459-6d761597-096f-442a-b4e8-f2980c9bf98b.png" width="70%" height="70%">

- 내부 element들을 감쌀 div container를 만들어주고, margin-left와 right에 auto로 가운데 정렬을 진행.
- border-radius는 모서리 부분을 둥글게 만들어 줄 수 있는 property이다.


```html
<body>
    <div class="container">
        <div class="profile">Profile image</div>
        <div class="account">Account Info</div>
    </div>
</body>
```

```css
.container {
    width: 940px;
    height: 200px;
    background-color: #fafafa;
    border: 1px solid #dbdbdb;
    border-radius: 10px;
    margin: 20px auto;
    display: flex;
}

.profile {
    width: 290px;
    height: 100%;
}
```

<img src="https://user-images.githubusercontent.com/95380638/149881738-e65f1ca1-45f1-4879-81ca-bbd854822dd5.png" width="70%" height="70%">

- 그 다음, 부모 div에 display:flex를 적용하고, 2개의 div element를 만들어서 Profile과 account 부분을 구분해준다.






- 부모 div container에 display:flex를 적용하고, 안에 있는 -> div profile에다가도 display:flex를 적용해서 -> 자유롭게 배치할 수 있다.
