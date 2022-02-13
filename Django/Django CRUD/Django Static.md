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





- 이렇게 html 파일위에다가 {% load static %} 를 적어줘서 static tag를 사용해야 한다. static이라는 tag를 활성화시키는 것이다.
- django template에서 static과 같이 특별한 기능을 가지고 있는 키워드를 우리는 tag라고 부른다.(html에서의 개념과는 다름)
