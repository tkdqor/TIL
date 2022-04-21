## Read로 데이터 조회하기 (2)
- 1개의 글의 상세 내용을 불러오도록 해보자. 글이 여러개 있는 메인 화면에서 1개의 글 속에 "더보기" 버튼을 클릭하면 상세 페이지로 갈 수 있게끔 하자. post_card.html로 가서

```html
...
<div class="card_content">
            <span class="card__like-num">좋아요 00개</span>
            <div class="card__main-text">
                <p><span
                        class="card__user-name">{{ post.writer }}</span>{{ post.content|truncatechars:35 }}<span><a href="{% url 'posts:post-detail' post.id %}">더보기</a></span>
                </p>
            </div>
```
  
- 여기서 더보기 부분에 a element를 사용해서 \<a href="/posts/{{post.id}}/">더보기\</a> 이렇게 디테일 페이지로 전환될 수 있도록 설정. 
  - 근데 urls.py에서 post-detail name를 설정해주면,  \<a href="{% url 'posts:post-detail' post.id %}">더보기\</a> 이렇게 해줄 수 있다. 


- **이제 views.py로 가서 post_detail_view를 수정해보자.**

```python
...
def post_detail_view(request, id):
    post = Post.objects.get(id=id)        # 게시글 1개에 대한 데이터 가져오기
    context = {
        'post': post,
    }

    return render(request, 'posts/post_detail.html', context)
```

- 여기서는 글 1개를 가져오는 것이기 때문에 get 메소드로 불러와주면 된다.


- **다시 post_card.html를 보면, 디테일 페이지에서도 "더보기" 버튼이 출력되는데, 이 부분을 수정해보자. 그래서 먼저 post_detail.html도 수정해보자.**

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}글 상세{% endblock %}

<!-- content block -->
{% block content %}
<h1>Post 상세 화면</h1>
{% include 'mixin/posts/post_card.html' with detail=True %}
{% endblock %}
```

- **이렇게 디테일 페이지에서 card template를 include 할 때, detail이라는 변수를 True로 해서 넘겨준다.** 그리고나서 이제 post_card.html를 보면,

```html
 <!-- 포스트 내용 -->
        <div class="card_content">
            <span class="card__like-num">좋아요 00개</span>
            <div class="card__main-text">
                <p><span
                        class="card__user-name">{{ post.writer }}</span>
                        <!-- 디테일 페이지이면 내용 전부를 보여주기 -->
                        {% if not detail %}
                            {{ post.content|truncatechars:35 }}
                        {% else %}    
                            {{ post.content }}
                        {% endif %}
                        <!-- 디테일 페이지가 아니면 버튼 보여주기 -->
                        {% if not detail %}
                        <span><a href="{% url 'posts:post-detail' post.id %}">더보기</a></span>
                        {% endif %}
                </p>
            </div>
```

- **위와 같이 {% if not detail %} 이렇게 입력해서 디테일 페이지가 아닐 때만 버튼을 보여줄 수 있도록 한다.**
  - 그리고 추가로 디테일 페이지이면 post.content를 짜르는 게 아니라 다 보여주자. 이것도 if문으로 설정 가능하다. 디테일 페이지가 아니라면 짤라서 보여주고 / 디테일 페이지라면 전체 내용인 post.content를 다 보여주도록 설정한다.

- 조금 더 정확하게 코드를 구성하려면, index.html에서는 detail 변수를 False로 넘겨주면 된다.

```html
{% extends 'base.html' %}
{% load static %}

<!-- title block -->
{% block title %}홈{% endblock %}

<!-- content block -->
{% block content %}
<main>
    <div class="card-list">
        {% for post in post_list %}
            {% include 'mixin/posts/post_card.html' with detail=False %}
        {% endfor %}
    </div>
</main>
{% endblock %}
```


* * *

### 댓글도 
