## Create로 데이터 생성하기 (2)
- 먼저 liongram 프로젝트에서 url 매칭을 해주자. 프로젝트 디렉터리 내부에 templates 디렉터리가 있고 그 안에 index.html이 있다.

- 우리가 서버를 구동시키고 브라우저를 새로고침하면, VSCode 터미널에 요청이 굉장히 많이 뜨게 된다. 
  - 브라우저에서 우클릭하고 개발자도구를 키면 -> 그리고 네트워크를 클릭하고 새로고침을 하면 파일들이 뜨게 된다. 우리가 localhost에게 Http 요청을 날린 것이다. -> 그래서 응답을 받아 html을 
    가져온 것이다. 그 안에서 CSS도 우리가 요청해서 받아온 것이다. 그래서 이렇게 한 번 요청했을 때 터미널에 많이 뜨게 되는 것이다. (파일들이 많으니까)
  - **근데, 다시 새로고침하면 터미널에 많이 뜨지는 않는다. 그 이유는, 이 페이지 자체에 일부 정적 데이터들은 우리가 캐싱을 하고 있다. 즉, 임시 보관을 하고 있게 된다. 그렇기 때문에 요청이 
    안 날라오게 되는 것이다.**
  - **근데 우리가 새로고침을 할 때, 현재 캐싱을 지우고서 새로고침을 하는 방법도 있다. 윈도우에서는 컨트롤 + shift + R이고 Mac에서는 command + shift + R이다. 그래서 이렇게 새로고침을 하면 다시 많은 요청이 날라온다.**


### urls 설정
- posts 앱 내부에 urls.py에서 name를 설정해주기.

```python
app_name = 'posts'

urlpatterns = [
    path('', post_list_view, name='post-list'),
    path('create/', post_create_view, name='post-create'),
    path('<int:id>/', post_detail_view),
    path('<int:id>/edit/', post_update_view),
    path('<int:id>/delete/', post_delete_view),
]
```

- 이렇게 설정하면 base.html에서 a element를 사용할 때, \<a href="{% url 'posts:post-create' %}">링크\</a> --> 이렇게 사용할 수 있다.


### template 설정
- 프로젝트 디렉터리 내부 templates 디렉터리 - posts 디렉터리 - post_form.html를 수정해보자. 여기서 form 태그를 추가한다.

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}글 입력{% endblock %}

<!-- content block -->
{% block content %}
<h1>Post 입력 화면</h1>
<form action="{% url 'posts:post-create' %}" method="POST">
    {% csrf_token %}
    <!-- 이미지 입력 -->
    <div>
        <label for="id_image">이미지</label>
        <input type="file" name="image" accept="image/*" id="id_image">
    </div>
    <!-- 내용 입력 -->
    <div>
        <label for="id_content">내용</label>
        <textarea name="content" id="id_content" cols="30" rows="10"></textarea>
    </div>
    <!-- 데이터 전송  -->
    <div>
        <input type="submit">
    </div>
</form>
{% endblock %}
```

- form의 method는 POST로, 데이터를 생성하는 것이니까 설정했다. 
- 그리고 첫번째 div에는 이미지를 위한 label과 input를 설정해준다. type은 file로 설정.
- 두번째 div는 글 내용을 입력하기 위한 코드이다. 여기서는 input이 아니라 textarea element를 사용한다. 우리가 Post 모델의 content 필드를 TextField로 했기 때문에 긴 글이 필요하다.
- 마지막 div는 데이터를 보내기 위해 input의 type를 submit으로 수정해준다.
