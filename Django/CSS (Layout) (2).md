## CSS (Layout) (2)

### bootstrap CDN
- bootstrap CDN를 검색해보면, https://www.bootstrapcdn.com/ 여기서 자바스크립트를 펼치고 html을 복사해서 post_list.html head element에 붙여넣으면 된다. 부트스트랩 전에다가 넣어주기.
  - 그리고나서 개발자도구 - Console에 보면 자바스크립트 얘기가 나올 수 있다.

- CSS Framework는 자바스크립트가 필요하고 내부적으로 jquery가 필요하다. https://releases.jquery.com/ 해당 사이트에서 2버전을 받아보자.  uncompressed 클릭 후 링크 주소 복사하고 head element에 bootstrap 
  전에다가 넣어준다.
  
- https://www.bootstrapcdn.com/bootswatch/ 여기 bootstrap CDN에서 bootswatch가 있는데 사용해보자. 이때는 기존의 bootstrap link를 지워주고 붙여넣기.



### template 상속
- 앱 내부 디렉터리에 layout.html를 만들어주기. 그리고 post__list.html 내용을 다 복사해주자. 그 다음, post_list만의 body 내용을 다 지운다.
- 그리고 django template tag 중에서 block 이라는 tag가 있다. 그래서 layout.html에서

```html
<head>
  <title>{% block title %}{% endblock title %}</title>
</head>

...
<body>
  {% block content %}
  {% endblock content %}
</body>  
```

- endblock에도 content라는 이름을 써주는 이유는 어떤 block에 대한 것인지 알 수 있게 하기 위함이다.
- **block을 사용하게 되면 현재 template이 부모가 되고 -> 현재 레이아웃을 post_list 등 다른 template에 대한 부모로 지정하는 것이다.**
  - **그래서 자식 template에서는 -> 맨 위에다가 {% extends 'instagram/layout.html' %} 이렇게 상속받을 수 있다.**
  - **그리고 자식 template은 부모를 상속받고 부모가 정의한 block를 자식이 구현하는 것이다. 자식 template 안에서 {% block content %} {% endblock content %}를 사용해서 내용을 채워주자.**

```html
{% extends 'instagram/layout.html' %}
{% load bootstrap4 %}                 <!-- 만약 설치했다면 코드 추가 -->

{% block title %}
    Instagram / Post List
{% endblock title %}

{% block content %}

    <!-- 검색창 만들기 -->
    <form action="" method="GET">
        <input type="text" name="q" value="{{ q }}" />
        <input type="submit" value="검색"/>
    </form>


    <table class="table table-bordered table-hover">
        <tbody>
            {% for post in post_list %}
                <tr>
                    <td>
                        {{ post.pk }}
                    </td>
                    <td>
                        {% if post.photo %}
                            <img src="{{ post.photo.url }}" style="width: 100px;" />
                        {% else %}
                            No Photo     
                        {% endif %}
                    </td>
                    <td>
                        <a href="{{ post.get_absolute_url }}">
                            {{ post.message }}
                        </a>
                    </td>
                </tr>
            {% endfor %}    
        </tbody>    
    </table>
    {{ is_paginated }}
    {{ page_obj }}

{% endblock content %}
```

- 이렇게 layout.html을 상속받아서 post_list.html을 깔끔하게 정리할 수 있다. 마찬가지로 detail.html도 진행할 수 있다. 
- 참고로, 부모가 정한 레이아웃을 수정할 수 없고 오직 부모가 정해놓은 block에서만 수정을 할 수 있다.

```html
{% extends 'instagram/layout.html' %}

{% block title %}
    Post Detail pk#{{ post.pk }}
{% endblock title %}

{% block content %}

    <h2>Author: {{ post.author }}</h2>

    {% if post.photo %}
        <div>
            <img src="{{ post.photo.url }}" alt="" />
        </div>
    {% endif %}

    {{ post.message }}
    <hr>
    <a href="{% url 'instagram:post_list' %}" class="btn btn-primary">
        목록
    </a>

{% endblock content %}
```

- post_detail.html은 다음과 같이 설정할 수 있다.


### getbootstrap 이용하기
- 사이트에 가서 example에 가면 여러가지 샘플이 있다. https://getbootstrap.com/docs/5.1/examples/
  - 그 중, 다음과 같은 예시도 있고, https://getbootstrap.com/docs/5.1/examples/album/ 사용하고 싶은 예시를 클릭한 다음 -> 우클릭 - 페이지 소스 보기 클릭 -> 여기서 다양한 소스들을 볼 수 있다.
  - **그리고 더 정확하게는, 예시 화면에서 개발자 도구를 켜고 -> 해당 요소, nav 등을 클릭한 다음 -> 오른쪽 코드에서 우클릭 - Copy - Copy outerHTML 을 선택하면 복사가 된다.** 그 다음, 해당 내용을 우리의 layout.html에 붙여넣으면 바로 똑같이 사용할 수 있다.

```html
<body>
    <!-- navbar 설정 -->
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
        <div class="container-fluid">
          <a class="navbar-brand" href="#">Carousel</a>
          <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarCollapse" aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav me-auto mb-2 mb-md-0">
              <li class="nav-item">
                <a class="nav-link active" aria-current="page" href="#">Home</a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="#">Link</a>
              </li>
              <li class="nav-item">
                <a class="nav-link disabled">Disabled</a>
              </li>
            </ul>
            <form class="d-flex">
              <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
              <button class="btn btn-outline-success" type="submit">Search</button>
            </form>
          </div>
        </div>
    </nav>

    {% block content %}
    {% endblock content %}
</body>
```

- **nav element의 class attribute 중, fixed-top이라는 항목을 사용하게 되면 -> 이 nav 항목이 상단 공중에 떠 있게 된다. 즉, 상단에 고정이 된다.**
  - **이럴때는 body element에 padding-top: 5rem; 를 주게 되면 상단에 nav가 고정되고 안에 항목들이 숨겨지지 않게 된다.**

* * *
- bootstrap에서는 그리드 시스템이라는 것이 있는데, 한 행을 12칸으로 나눠서 그리드를 처리하고 있다.
- layout.html에다가 nav 밑에 
```html
    <div class="container">
        <div class="row">
            <div class="col-sm-12">
                {% block content %}
                {% endblock content %}
            </div>
        </div>
    </div>
```

- 다음과 같이 div element로 감싸주자. col-sm-12는 small 디자인에서 12칸을 차지하겠다는 의미이다.
