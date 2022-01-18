## CSS Instagram 예시 (2)

```html
<!-- 다른 스토리 정보 영역 -->
    <div class="container2">
        
    </div>
```

```css
/* 다른 스토리 정보 영역 */
.container2 {
    width: 940px;
    height: 200px;
    background-color: #fafafa;
    border: 1px solid #dbdbdb;
    border-radius: 10px;
    margin: 20px auto;
    display: flex;
}
```

<img src="https://user-images.githubusercontent.com/95380638/149899881-b1402e31-9cb2-4ade-8abf-13d5919b1316.png" width="70%" height="70%">

- 이렇게 위의 div container와 동일한 CSS를 적용한 div container2를 만들기.


```html
<!-- 다른 스토리 정보 영역 -->
    <div class="container2">
        <div class="story-container">
            <div class="story-img-frame">
                <img class="story-img" src="http://www.medigatenews.com/file/news/109860" alt="Story Image">
            </div>
            <div class="story-title">
                Story Title
            </div>
        </div>
    </div>
```

```css
.story-img-frame {
    width: 80px;
    height: 80px;
    border: 3px solid #ebebeb;
    border-radius: 50%;
    overflow: hidden;
}

.story-img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}
```

<img src="https://user-images.githubusercontent.com/95380638/149902879-393b9cc4-daca-4156-b847-30658a385505.png" width="70%" height="70%">

- 스토리 하나를 구성할 div를 하나 만들고 story-container라는 class로 설정하기.
  - 그리고 그 하나의 div에서도 -> 이미지가 들어갈 div story-img-frame와 제목이 들어갈 div story-title로 구분해주기.
  - div story-img-frame와 그 안에 img를 넣고 img도 story-img라는 클래스를 설정.
  - css는 예시 (1) 같이 이미지와 그 이미지를 감싸는 div에 대한 css를 적용해주자. 작은 사이즈를 위해 div story-img-frame의 크기를 줄여주기.






- 이미지와 타이틀을 모두 감싸고 있는 div story-container에 대해 width와 hegiht를 설정하기.
- 그리고 타이틀이 위에 너무 붙어있으니 div story-title에 대해 padding값을 설정하기.
- 이미지와 타이틀이 모두 왼쪽으로 치우쳐져 있을 때는 -> 그것들을 감싸는 div element에 대해 display:flex로 설정하자.
  - 그래서 여기서는 div story-container에다가 display:flex 적용하고 flex-direction: column; 이렇게 적용해서 세로로 조정할 수 있도록하기.
  - 그리고 가로 정렬은 align-items: center; / 세로 정렬은 justify-content: center; 로 설정하면 가운데로 정렬이 된다.
