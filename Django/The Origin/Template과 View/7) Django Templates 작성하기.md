## Django Templates 작성하기


### 상속 관련 templates tag 사용해보기
- 여러 html 파일에 들어가 있는 공통된 코드를 없애보기 
- **루트 디렉터리 위치에 있는 templates 디렉터리 내부에 base.html를 생성해보자.** 그리고 index.html에 있는 코드들을 그대로 복사해서 가져온다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}{% endblock %} | 라이언그램</title>
</head>
<body>
    <div>
        {% block content %}
        {% endblock %}
    </div>
</body>
</html>
```

- **base.html이라는 파일 자체가 상속되었을 때, 상속하는 하위 html에서 어디에다가 코드를 넣을것인지 정의하는 게 바로 block이다. 위에서처럼 block 다음에 content라는 이름을 정해주고 tag를 입력해보자. 
  끝낼 때는 endblock 이라는 이름으로 tag를 작성해서 상속이 되는 부분을 정해줄 수 있다.**
  - title도 block을 사용해서 자식 html마다 다르게 설정할 수 있게 해보자.
  
* * *
- 이제 상속을 받는 자식 html에서는 어떻게 하는지 알아보자. 예시로 index.html의 코드를 수정해보자.

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}인덱스{% endblock %}

<!-- content block -->
{% block content %}
<h1>인덱스 화면</h1>
    <ul>
        <li><a href="{% url 'posts:post-list' %}">게시글 목록</a></li>
    </ul>
{% endblock %}
```


- **{% extends 'base.html' %} 이 tag는 상속을 해당 html파일에서 받겠다는 의미이다.**
  - **그리고 {% block content %} 과 {% endblock %} 사이에 상속받는 html에만 들어가는 코드들을 입력해주면 된다. title도 {% block title %} 과 {% endblock %} 사이에 상속받는 html에만 들어가는 제목을 입력해주면 된다.**

- 여기까지 설정해주고 브라우저에 인덱스 페이지를 들어가면 상속이 잘 이루어진 것을 확인해볼 수 있다.

- 이제 나머지 post_list.html / post_detail.html / post_form.html / post_confirm_delete.html도 전부 base.html을 상속받을 수 있도록 코드를 수정하자.
