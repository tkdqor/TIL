## Instagram 프로필 따라해보기

### 프로필 사진과 계정 정보를 담고 있는 container 구성

### 프로필 사진 영역
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

* * * 

### 계정 정보 영역
```html
<!-- 계정 정보 영역 -->
        <div class="account">
            <h1>Gattaca</h1>
                <ul>
                    <li>게시물 6,626</li>
                    <li>팔로워 384백만</li>
                    <li>팔로우 48</li>
                </ul>
            <h2>Gattaca</h2>
            <p>A genetically inferior man assumes the identity of a superior one in order to pursue his lifelong dream of space travel.</p>
            <a href="https://www.imdb.com/title/tt0119177/">About Gattaca</a>
        </div>
```

```css
/* 계정 정보 영역 */
.account h1 {
    font-size: 28px;
    font-weight: normal;
}

.account h2 {
    font-size: 16px;
    font-weight: bold;
}
```

<img src="https://user-images.githubusercontent.com/95380638/149890084-8f32ae1d-c371-41bc-adb7-4524d1424d84.png" width="70%" height="70%">

- 계정 정보 영역을 표시하는 div ccount안에 -> h1 / ul / h2 / p element를 사용해서 내용 구성.
- font-weight라는 property는 글씨 두께를 조절할 수 있음.


```css
/* CSS Reset */
* {
    padding: 0px;
    margin: 0px;
    box-sizing: border-box;
}
```

<img src="https://user-images.githubusercontent.com/95380638/149892347-623bf276-4046-4210-aad4-807d7c8bc1d3.png" width="70%" height="70%">

- 우리가 따로 여백을 주지 않았는데도 여백이 있는 것을 확인할 수 있다. h1의 경우 우리가 따로 주지 않아도 기본적으로 여백을 스스로 가지고 있다. ul나 h2, p도 자체적인 padding과 margin를 가지고 있다. 
- 이처럼 여러 html element에는 우리가 별도로 padding이나 margin을 주지 않아도 기본적으로 세팅되어 있는 값들이 있다. 브라우저마다 이러한 설정 값들이 조금씩 차이나기 때문에 사용자가 어떤 브라우저를 사용하느냐에 따라 웹 페이지가 서로 다르게 보일 수 있다.
- 그래서 CSS 작업을 할 때는, padding이나 margin의 값들을 전부 없애주고 시작하는 것이 좋다. 이렇게 기본 값들을 리셋하는 것을 **CSS Reset**이라고 부른다. 또한, 완벽하게 Reset하지 않고 최대한 통일을 시키되 기본적으로 가지고 있으면 좋은 것들은 최대한 살리려고 하는 방식을 **CSS Nomalization** 이라고 한다.
  - 지금은 가장 간단한 CSS Reset 방식으로 CSS 파일에서 *로 padding과 margin을 0으로 설정하고 CSS box를 border기준으로 설정하자.
  - 그리고 CSS는 우선순위가 있기 때문에 CSS 파일에서 가장 위쪽에 CSS Reset 코드를 입력하자.
