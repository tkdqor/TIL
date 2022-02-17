## Bootstrap
- Bootstrap은 트위터에서 개발한 오픈소스 프론트엔드 라이브러리.
- Table이나 Form을 비롯해서 사람들이 많이 쓰는 레이아웃에 대한 스타일링 방법을 제공한다. 또한, Bootstrap은 CSS 뿐만 아니라 JavaScript도 사용되었다.
- 그래서 Bootstrap을 사용하려면 -> Bootstrap이 사용하고 있는 CSS파일과 JavaScript파일을 모두 연동시키고 import 해줘야 한다.


## Bootstrap 준비
- 구글에 Bootstrap을 입력하고 해당 페이지에 들어가서 index 페이지에 있는 Get started를 클릭하기.
- Bootstrap를 활용하기 위한 CSS파일을 연동하기 위해서 link element를 복사하고 html 파일에 다른 css 연동보다 위에 붙여넣어주기.

```html
{% load static %}

<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <!-- Bootstrap CSS파일 연동 -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
    <link rel="stylesheet" href="{% static 'style.css' %}">
    {% block style %}
    {% endblock %}
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Heestagram</title>
</head>
<body>
    {% block content %}
    {% endblock %}

    <!-- Bootstrap JS파일 연동 -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-ka7Sk0Gln4gmtz2MlQnikT1wXgYsOg+OMhuP+IlRH9sENBO0LRn5q+8nbTov4+1p" crossorigin="anonymous"></script>    
</body>
</html>
```

- 이렇게 base.html에 CSS 링크를 추가해주자. 그리고 Javascript파일의 경우는 Bundle 방식과 Separate 방식이 있는데 지금은 Bundle 방식을 사용하기 위해 Javascript를 연동하기 위한 script element를 복사해주자.  
  그리고 복사한 코드를 base.html의 body element의 end tag 직전에다가 script element를 붙여넣어주면 된다. 


### Bootstrap Table 이용해보기
- Bootstrap에 table을 입력하면 꾸밀 수 있는 방법이 나열된다.
- Bootstrap은 기본적으로 Bootstrap 라이브러리 내부에 있는 CSS의 룰들을 그대로 활용하면 된다. Bootstrap 페이지에 나와있는 클래스들을 적용만 시켜주면 예시에 나와있는 요소들을 만들 수 있다. 페이지에 나와있는 예시 코드의 클래스 부분만 복사해서 우리의 index template에 추가해보자.

```html
{% block content %}

<div class="container">    
    <h1>Posts</h1>

    {% if posts %}
     <table class="table">
         <thead class="table-dark">
     ...
```

- 이렇게 기존의 table element에 class="table" / thead element에는 class="table-dark" 이러한 클래스를 붙여만 줘도 이미 짜여진 테이블 디자인을 사용할 수 있다.
  - 그런데, Bootstrap에는 세로 실선이 없었는데 우리가 적용했을 때는 세로 실선이 생겼다. 이건 우리가 기존에 작성한 posts 앱 내부의 style.css에서 table, td, th 에다가 전부 border를 적용해주었기 때문이다. 그래서 이걸 지워주면 Bootstrap과 같이 세로 선이 사라진다.

- 즉, Bootstrap이 가이드하는 방식대로 그대로 사용할 수도 있고, 여기에 우리의 CSS를 입혀서 사용할 수도 있다. 
  - 현재까지는 table이 너무 화면에 꽉 차게끔 변경이 되었다. 이 문제를 해결하기 위해 -> index template의 {% block content %} 부터 {% endblock %} 까지의 코드들을 전체적으로 감싸는 div element를 만들어보자.
  - 그리고 div element에다가 class="container" 라고 추가해주면 -> bootstrap에서 정의하고 있는 container가 적용이 되어 웹 페이지 사이즈에 따라서 반응하는 table를 만들 수 있게 된다.


### Bootstrap으로 페이지 구성하기 - Form element
- 일단 글쓰기 페이지인 new.html을 수정해보자.
```html
{% block content %}

<div class="container">

    <h1>New Post</h1>

    <form method="POST" action="{% url 'posts:create' %}">
        {% csrf_token %}

        <div>
            <label for="author">Author</label>
            <input id="author" type="text" name="author">
        </div>
        
        <div>
            <label for="body">Body</label>
            <textarea id="body" cols="40" rows="10" name="body"></textarea>
        </div>

        <input type="submit" value="작성하기">   
    </form>
   
    <a href="{% url 'posts:index' %}">목록</a>

</div>    

{% endblock %}    
```

- block content 사이의 내용을 감싸는 div.container 생성 -> 그러면 여백을 가진 레이아웃이 구성된다.
- **bootstrap에서 form를 검색해보기.**
  - 맨 처음에 나오는 예제를 보면, label element에 class="form-label"이 설정되어있고 / input element에는 class="form-control" 이라는 클래스를 부여함으로서 CSS를 구성할 수 있다고 나와있다. 이러한 클래스들을 우리의 html element에도 적용시켜보자.

```html
...
<div>
     <label class="form-label" for="author">Author</label>
     <input class="form-control" id="author" type="text" name="author">
</div>
```

- 그리고, textarea의 경우에는 Form control이라는 별도의 문서에서 설명하고 있다고 한다. 똑같이 class="form-control" 적용해보자.

```html
...
<div>
    <label class="form-label" for="author">Author</label>
    <input class="form-control" id="author" type="text" name="author">
</div>
        
<div>
    <label class="form-label" for="body">Body</label>
    <textarea class="form-control" id="body" cols="40" rows="10" name="body"></textarea>
</div>
```


### Bootstrap으로 margin 설정하기
- margin를 주고자 하는 html element에다가 class= 이렇게하고 margin을 의미하는 m을 적고 / top, down, left, right 방향을 적어주면 된다. / 그리고 얼만큼 주고 싶은지 - 하고 숫자를 적자.
  - 만약, 아래쪽에 margin를 주고 싶다면 class="mb-3" 이렇게 하면 되는 것이다.

```html
<div class="mb-3">
      <label class="form-label" for="author">Author</label>
      <input class="form-control" id="author" type="text" name="author">
</div>
```

- 이렇게 bootstrap을 이용해서 margin를 설정해볼 수 있다.


### Bootstrap으로 button 설정하기
- bootstrap의 form를 검색하고 overview를 클릭해보면, 우리가 봤던 form 예제에 버튼 코드도 볼 수 있다.
  - button element를 사용하고 있고 type이 submit인 것을 확인할 수 있다. 또한, value attribute를 사용하지 않고 content로 적어주면 버튼에 나타나게 된다.
  - 이 button element로 input element를 교체해보자.

```html
<div class="mb-3">
     <input type="submit" value="작성하기">   
     <button type="submit" class="btn btn-primary">작성하기</button>
</div>
```

- **이렇게 input element이든, button element이든 둘 다 type이 submit이기만 하면 -> form element 내부에서 사용자가 입력한 데이터가 HTTP Request, 전송을 유발시키는 버튼으로써 활용할 수 있다.**



### Bootstrap의 class="container"
- bootstrap에서 Layout - Containers를 누르면 -> 우리가 사용했던 container에 대한 설명이 나와있다. https://getbootstrap.com/docs/5.1/layout/containers/ 
- 그리고 Utilities - Spacing를 누르면 -> margin과 padding에 대한 내용이 나와있다. https://getbootstrap.com/docs/5.1/utilities/spacing/
  - m은 margin / p는 padding / t는 top / b는 bottom / s는 left / e는 right 이렇게 사용하게 된다.
 
 
### Bootstrap에서 column 설정으로 레이아웃 구성하기
- 레이아웃 상, 가로의 길이와 구성을 수정하기 위해서는 요소들을 담고있는 div element 등에 
```html
<form method="POST" action="{% url 'posts:create' %}">
        {% csrf_token %}

        <div class="mb-3 col-md-6">
            <label class="form-label" for="author">Author</label>
            <input class="form-control" id="author" type="text" name="author">
        </div>
        
        <div class="mb-3 col-md-6">
            <label class="form-label" for="body">Body</label>
            <textarea class="form-control" id="body" cols="40" rows="10" name="body"></textarea>
        </div>
        ...
```    

- 다음과 같이 class 안에 추가로 설정을 해준다.
  - **col은 column의 약자이고 / md는 medium의 약자이다.**
  - **기존의 div element가 차지하고 있던 공간을 12등분 한 다음에 -> 각각의 공간을 column이라고 한다. 즉, col-md-6는 총 12개의 column 중에서 6개의 column에 해당하는 만큼만 너비를 차지하겠다는 의미이다.**

- **그럼 md는 무엇일까?** Bootstrap 홈페이지에서 Layout - Containers를 가보면,
  - **Medium은 768px 이상인 경우를 의미하고 있다. 즉, 브라우저의 전체 사이즈, 너비가 768px 이상인 경우를 의미한다.**
  - **그래서 개발자도구에서 화면 사이즈를 조절하면 -> 현재 웹 페이지의 너비를 바로 확인할 수 있다. 이 브라우저의 너비가 768px 보다 작아지는 순간, col-md-6이라는 스타일이 적용되지 않는다.**
  - **즉, 기본적으로 div element는 부모 element의 너비를 전부 다 차지하게 되는데, 디바이스의 너비가 medium, 768px 이상일 경우에는 -> 부모의 너비를 12등분 한 column를 활용해서 총 6개의 column, 즉 부모 element 너비의 반만큼을 차지하겠다는 것이고 단, 디바이스의 너비가 768px 이상일 경우에만 그렇게 하겠다는 설정이다.**
  - 그래서 개발자도구를 열고 웹 페이지의 너비가 768px보다 클 경우에는 부모 element에 반만큼만 공간을 차지하게 되고 / 웹 페이지의 넓이가 768px보다 작을 경우에는 부모 element의 너비만큼 다 차지하고 있는 것을 볼 수 있다.
 
 
 

- 참고 블로그 : https://datamoney.tistory.com/149
- https://dinfree.com/lecture/frontend/122_css_6.html 해당 블로그 2개 꼭 읽어보기!
- https://www.airbnb.co.kr/
