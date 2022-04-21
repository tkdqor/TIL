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
