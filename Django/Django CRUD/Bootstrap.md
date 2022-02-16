## Bootstrap
- Bootstrap은 트위터에서 개발한 오픈소스 프론트엔드 라이브러리.
- Table이나 Form을 비롯해서 사람들이 많이 쓰는 레이아웃에 대한 스타일링 방법을 제공한다. 또한, Bootstrap은 CSS 뿐만 아니라 JavaScript도 사용되었다.
- 그래서 Bootstrap을 사용하려면 -> Bootstrap이 사용하고 있는 CSS파일과 JavaScript파일을 모두 연동시키고 import 해줘야 한다.


## 실습 예시
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
