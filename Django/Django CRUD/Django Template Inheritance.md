## Django Template Inheritance
- django template을 구성할 때, 똑같이 반복되는 코드들이 많을 수 있다.
  - 기본적으로 HTML 문서 자체를 구성하기 위한 각종 메타 정보들은 페이지가 바뀌어도 변함이 없다. 만약 이러한 부분이 2천만 줄이 넘는 코드로 구성되어있다면, 매번 작성하기 어려울 것이다.

- 이러한 문제를 해결하기 위해, Template Inheritance(상속) 라는 방법이 존재한다.
  - 여러 template에서 공통적으로 사용하는 코드를 추출해서 하나의 base template으로 만들고 -> 이 base template을 상속 받아서 base template과 차이가 나는 부분만 작성하게끔 하자.
  - 여러 template이 만약 공통적인 style을 사용하고 있고, 화면 상단에는 Navigation / 하단에는 Footer 이렇게 구성되어 있다면 안에 contents만 다를 뿐 전체적인 틀은 똑같이 유지가 된다.

- 그래서, 공통적인 레이아웃에 해당하는 부분을 뽑아서 base.html로 구성하고 
  - 웹 페이지별로 차이가 나야하는 부분을 Contents Block이라는 특별한 영역으로 지정해준다. 그리고 각 페이지에서는 이 Contents Block에 해당하는 부분을 채워주면 된다.


### 실습해보기
- 먼저, posts 앱 디렉터리 -> templates -> posts -> 여기에 base.html 생성
- 그리고 기존의 index.html의 코드들을 복사해서 붙여넣고 모든 template에서 공통적으로 사용했던 부분만 남기기.
- 실제 차이가 날 부분은 -> {% block content %} 이렇게 실제 차이가 날 부분 시작에 적어주자. 그리고 마지막에는 {% endblock %} 이렇게 입력해주면 된다. 그래서 block의 시작과 끝을 명시적으로 표현한다.
  - 특별한 영역인 block를 만들어주겠다는 것. 그리고 해당 block의 이름은 content라고 지어주는 것이다.

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
<body>
    {% block content %}
    {% endblock %}
</body>
</html>
```

- 위와 같이 base.html을 구성했다면, 이제 각각의 template에서는 content라는 block에 들어갈 내용만 작성해주면 된다.
  - 그래서 실제로 base template을 상속받아서 content block에 해당하는 내용만 코드를 작성하려고 한다면 -> 각 template에서 base template을 상속받아서 만드는 template이라는 점을 코드로 입력해줘야 한다. 바로 **{% extends 'posts/base.html' %}** 이런식으로 해당 template이 posts 디렉터리 내부에 있는 base.html을 상속받는 중이라고 해주는 것이다.

- 또한, block이 꼭 하나가 아니고 여러개로 구성할 수도 있다. 그래서 상속받은 template에서 어떤 block에 내용을 넣을 것인지 지정해줘야 한다. base.html에서 입력했던 코드와 동일하게 다르게 입력하고자 하는 부분의 처음과 끝에 {% block content %} - {% endblock %} 이렇게 설정해주면 된다.

```html
{% extends 'posts/base.html' %}

{% block content %}

    <h1>Post detail</h1>

    
    {% if post %}
        <h2>작성자</h2>
        <p>{{ post.author }}</p>
        
        <h2>본문</h2>
        <p>{{ post.body }}</p>

        <h2>게시일</h2>
        <p>{{ post.created_at }}</p>
    {% else %}
        <p>No Post</p>
    {% endif %} 
       
    <a href="{% url 'posts:index' %}">목록</a>
    <a href="{% url 'posts:edit' post.id %}">수정하기</a>
    <a href="{% url 'posts:delete' post.id %}">삭제하기</a>

{% endblock %}
```

- 위의 코드는 detail.html 예시이다. 다른 template들도 이런 방식으로 코드를 변경해보자.

* * *

### 다양한 app이 있다고 가정하고 Template Inheritance 재설정하기
- 특정 app에서만 공통적으로 사용하는 template이 아니라, 모든 app에서 프로젝트 전반에 걸쳐 공통적으로 사용하고자 하는 template이 있을 수 있다.
  - 이 경우에는 앱 패키지 내부에 작성하는 것이 아니라 -> 프로젝트 루트 디렉터리 내부에다가 templates라는 디렉터리를 만든 다음에 namespacing 없이 바로 base.html를 생성하면 된다.
  - 이 base.html에서는 모든 app에 걸쳐서 사용할 공통된 레이아웃을 작성해주면 된다.

- 위에서 작성했던 app 내부의 base.html 코드를 복사해서 그대로 가져오자. 그리고 기존의 base.html는 삭제하기.
  - 그래서 결론적으로 base.html를 앱 내부에 있는 게 아니라 -> 앱 바깥에 프로젝트 레벨에서 template 디렉터리 내부에다가 구성을 했고 앱 패키지 내부에서 사용하는 다른 template에서는, 

```html
{% extends 'base.html' %}

{% block content %}

    <h1>New Post</h1>

    <form method="POST" action="{% url 'posts:create' %}">
        {% csrf_token %}
    ...
```      

- 위의 new.html 예시와 같이 {% extends 'posts/base.html' %} 이렇게 posts라는 namespacing를 해주는 게 아니라, {% extends 'base.html' %} 이렇게 수정해주면 된다. 다른 template들도 수정해주자.
- 그런데, 이 상태에서 서버를 실행시키고 보면 TemplateDoesNotExist라는 에러가 발생한다.
  - **django는 기본적으로 django 프로젝트에 등록되어있는 app 단위로 templates라는 이름을 가진 디렉터리를 검색하기 때문에, 우리가 방금 추가했던 -> app 내부에 있는 templates가 아니라, 프로젝트 루트에 위치해있는 templates라는 디렉터리는, app 하위에 존재하는 template이 아니기 때문에 검색 대상에서 제외된다.**
  - 그래서 이 문제를 해결하려면, django의 설정을 변경해서 -> django가 app 하위에 있는 templates 이외에도 프로젝트 루트에 있는 templates 디렉터리를 검색하게끔 해줘야 한다.
    - 프로젝트 이름의 디렉터리 -> ㄴㄷㅅ
