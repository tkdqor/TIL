## Bootstrap 추가 내용
- 부트스트랩은 반응형 웹사이트를 지원해준다.
- Get started 누르고 CSS와 JS 연결 코드를 각각 우리의 html가져와 준다.


### Bootstrap 동작원리
- 예를 들어 부트스트랩에서 버튼을 하나 가져온다고 하면,
```html
<button type="button" class="btn btn-primary">Primary</button>
```

- 이러한 코드를 볼 수 있다. 여기서 class="btn btn-primary -> 이러한 클래스 스타일이 적용되어있다. 이러한 스타일은 우리가 복사해서 붙여준 CSS 파일에서부터 오는 것이다. 이러한 링크를 CDN(Content Delivery Network)라고 부른다.
  즉, 제3의 어떤 누군가가 이 CSS 코드를 올려놓았고 우리는 해당 링크를 통해서 접속을 하게 되는 것이다. 
  
- **Bootstrap - Components 클릭해서 다양한 것들을 적용해볼 수 있다. 한 번씩 다 확인해보기.**
  - **이외에도 Content에서는 Images / Tables를 활용할 수 있고, Forms에서는 Form element와 Checks & Radios에서는 체크박스 / input / validation를 확인해보자.**
  - **그리고 Utilites에서는 Background / Borders / Overflow / Position / Sizing / 

### Layout - Grid
- 그리드는 엑셀처럼 행과 열이라고 볼 수 있다. 웹사이트는 사실 행과 열로 구성이 되어있다. Grid를 통해 반응형 웹사이트를 만들 수 있다.

- 예를 들어, bootstrap의 card라는 것의 코드를 복사해서 붙여넣으면 계속해서 아래에 1개씩 추가가 된다. 그런데 모두 card를 쇼핑몰처럼 한 줄에 나열하게끔 하려면 다르게 코드를 입력해야 한다.
```html
...
<body>
  
<div class="row">

    <div class="col">
        <div class="card" style="width: 18rem;">
            <img src="..." class="card-img-top" alt="...">
            <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
              <a href="#" class="btn btn-primary">Go somewhere</a>
            </div>
          </div>
    </div>
    <div class="col">
        <div class="card" style="width: 18rem;">
            <img src="..." class="card-img-top" alt="...">
            <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
              <a href="#" class="btn btn-primary">Go somewhere</a>
            </div>
          </div>
    </div>
    <div class="col">
        <div class="card" style="width: 18rem;">
            <img src="..." class="card-img-top" alt="...">
            <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
              <a href="#" class="btn btn-primary">Go somewhere</a>
            </div>
          </div>
    </div>
    <div class="col">
        <div class="card" style="width: 18rem;">
            <img src="..." class="card-img-top" alt="...">
            <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
              <a href="#" class="btn btn-primary">Go somewhere</a>
            </div>
          </div>
    </div>
    
</div>
    
</body>
```


- **먼저, 한 줄을 의미하는 div class="row"로 시작하자.**
  - **그리고 이 한 줄안에 class가 column인 div를 총 4개 넣어준다.** 그리고나서 이 각각의 column에 card에 해당하는 부트스트랩 코드를 넣어보자. 
  - 그러면 card가 한 줄에 4개로 잘 들어가 있는 것을 확인할 수 있다. 그리고 사이즈에 따라 한 줄에 보여주는 개수도 조절해서 반응형 웹사이트도 만들어준다.

- **또한, 그냥 column만 주는 게 아니라 사이즈를 지정해서 줄 수도 있다.**
  - **class="col-lg-3"** -> 이렇게 column은 맞는데 large 사이즈, 즉 화면 사이즈가 클 때는 3정도의 크기만 주겠다는 것이다.
  - **Bootstrap에서는 1줄의 크기를 -> 12이라고 본다. 그래서 내가 만약 한 줄의 크기를 6를 주면 반만 차지하게 된다. 그래서 한 줄이 12만 안넘기면 된다.**

- **그래서, 위와 같이 한 줄에 아이템이 4개가 있는 경우에는 -> 한 column의 사이즈는 3이 되면 된다.** 그래서 위의 코드에서 사이즈 설정을 디테일하게 다시 해주면,
```html
<div class="row">

    <div class="col-lg-3 col-md-6">
        <div class="card" style="width: 18rem;">
            <img src="..." class="card-img-top" alt="...">
            <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
              <a href="#" class="btn btn-primary">Go somewhere</a>
            </div>
          </div>
    </div>
    <div class="col-lg-3 col-md-6">
        <div class="card" style="width: 18rem;">
            <img src="..." class="card-img-top" alt="...">
            <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
              <a href="#" class="btn btn-primary">Go somewhere</a>
            </div>
          </div>
    </div>
    <div class="col-lg-3 col-md-6">
        <div class="card" style="width: 18rem;">
            <img src="..." class="card-img-top" alt="...">
            <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
              <a href="#" class="btn btn-primary">Go somewhere</a>
            </div>
          </div>
    </div>
    <div class="col-lg-3 col-md-6">
        <div class="card" style="width: 18rem;">
            <img src="..." class="card-img-top" alt="...">
            <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
              <a href="#" class="btn btn-primary">Go somewhere</a>
            </div>
          </div>
    </div>

</div>
```

- **그리고 추가로 사이즈가 md, 즉 medium 사이즈인 중간 사이즈라면 card를 한 줄에 2개씩 보여줄 수도 있다. 그러려면 한 column의 크기를 6으로 설정해주면 된다.**
  - 그래서 class="col-lg-3 col-md-6" 이렇게 설정해주면 크기에 따라 한 줄에 보여주는 항목의 개수가 달라진다.

* * *
### Bootstrap Example 사용해보기
- Bootstrap 사이트에서 Example를 클릭해보면 다양한 웹 사이트 구성들이 있다. 해당 예제 사이트들은 전부 Bootstrap의 컴포넌트들을 이용해서 만들었다. 우리는 이 예제들을 가져다가 사용할 수 있다.
- 마음에 드는 사이트 하나를 클릭하고 우클릭 -> 페이지 소스 보기(View page source)를 클릭해보면, 해당 사이트를 만들기 위해 사용된 모든 소스코드들이 다 나온다. 
  - 그래서 예시로, 해당 코드들을 전체 선택하고 복사한 다음 프로젝트에 붙여넣기를 해보자.
  - 그리고나서 바꿔줘야 할 부분들이 있다.

```html
<!-- Bootstrap core CSS -->
<link href="/docs/5.1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
```

- **위와 같이 해당 CSS는 부트스트랩 내부에서 사용한 것이기 때문에 href 앞에 http가 없다. 그래서 해당 1 줄의 link 코드는 전부 삭제하고, 우리가 처음에 bootstrap 적용할 때 복사하는 CSS 코드를 붙여주면 된다.**

```html
<!-- Bootstrap core CSS -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
```

- 이렇게 대체해주면 된다. 그리고 코드 끝부분에는 
```html
<script src="/docs/5.1/dist/js/bootstrap.bundle.min.js" integrity="sha384-ka7Sk0Gln4gmtz2MlQnikT1wXgYsOg+OMhuP+IlRH9sENBO0LRn5q+8nbTov4+1p" crossorigin="anonymous"></script>
```

- **다음과 같이 자바스크립트 코드가 있는데, 마찬가지로 http가 짤려있다. 그래서 해당 코드를 삭제하고 Bootstrap 처음 시작할 때 복사하는 JS 코드를 붙여주자.**

```html
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-ka7Sk0Gln4gmtz2MlQnikT1wXgYsOg+OMhuP+IlRH9sENBO0LRn5q+8nbTov4+1p" crossorigin="anonymous"></script>
```

- 그러면, 해당 예시 구조를 전부 자기의 프로젝트로 가져와서 사용할 수 있게 된다.
