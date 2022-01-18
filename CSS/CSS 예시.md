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


```css
.profile {
    width: 290px;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}
```

<img src="https://user-images.githubusercontent.com/95380638/149882460-e472bf4d-0fa3-4f50-82c4-835858bff088.png" width="70%" height="70%">

- 부모 div container에 display:flex를 적용하고, 안에 있는 -> div profile에다가도 display:flex와 justify-content, align-items를 적용해서 -> div profile만 정 가운데로 배치할 수 있다.


```html
<body>
    <div class="container">
        <div class="profile">
            <div class="profile-frame">
                <img id="profile-img" src="http://www.medigatenews.com/file/news/109860" alt="Profile Image">
            </div>    
        </div>
        <div class="account">Account Info</div>
    </div>
</body>
```

```css
.profile-frame {
    width: 170px;
    height: 170px;
    border: 5px solid #ebebeb;
    border-radius: 50%;
    overflow: hidden;
}

#profile-img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}
```

<img src="https://user-images.githubusercontent.com/95380638/149886714-40bd06f6-a959-480f-b441-0601b4446cb1.png" width="70%" height="70%">

- 이미지를 넣기 전에 -> 이미지를 감싸고 있는 div profile-frame를 하나 더 만들어주고 -> 해당 클래스 css에 width와 height를 설정해주고 block element의 정해진 영역을 빠져나가는 content를 가릴 수 있는 방법으로 overflow라는 property에다가 hidden를 설정해서 이미지가 빠져나오는 걸 방지하기.
- border-radius에 50%를 주게 되면, 정사각형의 절반을 반지름으로 하는 둥근 테두리가 되서 원이 만들어지게 된다.
- 그리고 이미지 자체인 img element에도 id를 설정하고 -> width와 height를 설정해준다.
- 또한, 이미지에 object-fit이라는 property에 cover라고 지정하게 되면 -> 이미지가 프레임에 맞게 조절이 된다.
