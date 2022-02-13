## Static(정적 파일)
- 웹페이지를 구성하는 CSS, image, Javascript 파일 등을 우리는 static 파일, 정적 파일이라고 부른다.
  - 서비스의 이용자가 바뀐다고 하더라도 해당 웹 페이지에서 사용하는 CSS, JavaScript 파일은 그 내용이 바뀌지 않을 것이다.
  - 홈페이지의 배너 이미지나, 백그라운드 이미지 등 변하지 않고 늘 고정적인 것들을 static 파일이라고 할 수 있다.
  - 반면, django template 처럼 데이터에 따라 변할 수 있는 경우는 동적인 페이지라고 할 수 있다.


### static 파일 사용하기
- template가 유사하게 static 파일도 app 단위로 구분하여, app 패키지 내부에 static 디렉터리를 생성하고 그 내부에 app이름과 동일한 이름을 가진 디렉터리를 만들어줘서 namespacing을 해준다. 그리고 그 안에 사용하고자 하는 파일들을 채워넣자.
- 실제 서비스를 런칭하는 프로덕션 환경에서는 app 단위로 구분되어있는 static 디렉터리들을 전부 다 하나로 합쳐서 사용하게 된다.


### django template에서 static 파일 사용
- django temtplate에서 static 파일을 사용하기 위해서는, 
```html
{% load static %}

<!DOCTYPE html>
<html lang="ko">
<head>
    <link rel="stylesheet" href="{% static 'posts/style.css' %}">
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Heestagram</title>
</head>

<img src=".." href="{% static 'posts/images/djf.jpg %}"
     
```

- 이렇게 html 파일위에다가 {% load static %} 를 적어줘서 static tag를 사용해야 한다. static이라는 tag를 활성화시키는 것이다.
- django template에서 static과 같이 특별한 기능을 가지고 있는 키워드를 우리는 tag라고 부른다.(html에서의 개념과는 다름)
  - static tag를 활성화했다면 -> 사용하고자 하는 static 파일의 경로를 지정할 때는 static 한 칸 띄우고 문자열로 static 파일의 경로를 지정해준다.

- 이렇게 경로를 지정하는 것은, 실제 서비스를 배포했을 경우에는 바탕화면의 파일이라는 경로를 사용할 수 없기 때문이다. 또한, 배포 과정에서는 app단위가 아닌 하나의 디렉터리 안에 모든 static 파일들을 관리하기 때문에 static 파일의 절대경로가 바뀔 수 있다. 그래서 특정 경로로 설정하지 않는 것이다.
  - 그래서, django template engine이 static tag를 확인했을 때 django의 환경설정에 기반해서 해당 static 파일의 위치를 계산하게끔 코드를 작성해야 한다.

- static 디렉터리 내부에 이미지도 관리할 수 있지만, 모든 이미지를 django의 static이라는 개념으로 처리해서는 안된다.
  - 백그라운드 이미지나 배너 이미지처럼 사용자가 누구든지 간에, 어떠한 상황이든 항상 변하지 않는 이미지들을 static으로 분류해서 관리하는 것이다.
  - 그래서 static 디렉터리 내부 -> app 이름 디렉터리 내부 -> images라는 디렉터리를 생성해서 이미지 파일들을 따로 관리하자.

- django template에서 style.css을 연동시켜서 사용하기 위해서는 head element에다가 link element를 사용해줘야 한다. 이 때 link element에 css파일 경로를 작성해줘야 하는데, <link rel="stylesheet" href="{% static 'posts/style.css' %}"> 이렇게 작성해주면 -> static tag를 사용해서 posts/style.css 라는 경로에 있는 걸 사용하겠다는 것이고, django template engine이 해당 코드를 읽게 되면, 해당 django 프로젝트 내부에 등록되어있는 모든 app에 대해서, 각 app 하위에 있는 static 디렉터리를 다 검색하게 된다.
  - 우리가 posts 디렉터리 내부에 있는 css 파일을 사용하겠다고 작성했기 때문에 무조건 posts 앱 내부에 있는 파일을 사용하게 된다. 
