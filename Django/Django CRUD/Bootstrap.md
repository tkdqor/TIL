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

9:20부터!



- 참고 블로그 : https://datamoney.tistory.com/149
- https://dinfree.com/lecture/frontend/122_css_6.html 해당 블로그 2개 꼭 읽어보기!
- https://www.airbnb.co.kr/
