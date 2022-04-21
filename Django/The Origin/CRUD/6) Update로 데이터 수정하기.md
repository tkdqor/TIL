## Update로 데이터 수정하기
- Update의 경우, Create와 Detail이 합쳐졌다고 보면 된다.
- **views.py의 post_delete_view 함수를 수정해주자.**

```python
...
def post_update_view(request, id):
    post = Post.objects.get(id=id)

    if request.method == 'GET':
        context = { 'post': post, }
        return render(request, 'posts/post_form.html', context)
    elif request.method == 'POST':
        pass
    
```


- **post_update_view는 GET방식으로 요청이 왔을 때는 수정할 수 있는 post_form.html를 보여주고 / POST방식으로 요청이 왔을 때는 실제로 데이터를 업데이트 해주는 기능을 적어주면 된다.**
- 먼저 업데이트할 글 하나를 get 메소드로 가져와준다. 
  - 그리고 GET 방식이면 그 데이터를 template에 보내준다.




- 추가로 post_card.html에는 수정하기 버튼을 만들어주자.

```html
<!-- 카드 헤더 부분 -->
    <div class="card__header">
        <img class="card__user-image" src="http://via.placeholder.com/32x32" alt="프로필이미지"/>
        <a href="#">
            <span class="card__user-name">{{ post.writer }}</span>
        </a>
        {% if request.user == post.writer %}
            <a href="{% url 'posts:post-update' post.id %}">수정하기</a>
        {% endif %}
    </div>
```

- **이렇게 a element로 수정하기 버튼을 생성해준다. 근데 이 버튼이 로그인된 유저와 글 작성자가 같을 때만 보여야 한다. 그래서 {% if request.user == post.writer %} 이렇게 로그인된 유저와 
  해당 글의 작성자가 같을 때만 a element가 보이도록 if문을 작성해준다.**
  

### Update 페이지 설정하기
- 이제 해당 글의 수정하기 버튼을 눌렀을 때, 뜨는 form 화면에서 데이터가 이미 들어가있게끔 설정해보자. post_form.html에서

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}글 입력{% endblock %}

<!-- content block -->
{% block content %}
<h1>Post 입력 화면</h1>
<!-- 이미지 전송을 위한 인코딩 방식 추가 -->
<form action="" method="POST" enctype="multipart/form-data"> 
    {% csrf_token %}
    <!-- 이미지 입력 -->
    <div>
        <label for="id_image">이미지</label>
        {% if post.image %}
        <p>현재 : <a href="{{ post.image.url }}">{{ post.image.name }}</a></p>
        <p>변경 : <input type="file" name="image" accept="image/*" id="id_image"></p>
        {% else %}
            <input type="file" name="image" accept="image/*" id="id_image">
        {% endif %} 
    </div>
    <!-- 내용 입력 -->
    <div>
        <label for="id_content">내용</label>
        <textarea name="content" id="id_content" cols="30" rows="10">
            {% if post %}
                {{ post.content }}
            {% endif %}    
        </textarea>
    </div>
    <!-- 데이터 전송  -->
    <div>
        <input type="submit">
    </div>
</form>
{% endblock %}
```

- **이렇게 textarea element에서 {{ post.content }} 내용이 들어가있도록 설정하자. 다만, post_form.html가 글을 생성할 때도 랜더링되는 페이지이기 때문에, {% if post %}로 글을 수정하는 경우에만 content가 뜨도록 설정하자.**
- **이미지 부분도 해당 게시글의 이미지가 있는 경우에는 --> a element가 뜨고 클릭하면 사진을 볼 수 있게끔 하고, 이미지가 있든 없든 input element로 사진을 넣을 수 있게 설정해준다.**
- 그리고 form의 action은 공백으로 두어서 post_form.html로 보낼 수 있도록 하면 된다.


