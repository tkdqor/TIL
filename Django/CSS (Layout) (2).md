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
  ...
  ...
  {% endblock content %}
</body>  
```

- endblock에도 content라는 이름을 써주는 이유는 어떤 block에 대한 것인지 알 수 있게 하기 위함이다.
- block이라는 것은 -> 현재 레이아웃을 post_list에 대한 부모로 지정하는 것이다.
