## Reverse for '...' with arguments '('',)' not found. 1 pattern(s) tried
- 해당 오류의 경우, 특정 id값을 찾지 못했다는 의미이다. 


- **그래서, template에서는**

```html
<a href="{% url 'posts:post-detail' post.id %}">더보기</a>
```

- **다음과 같이 id값을 정확하게 입력했는지, urlpattern name이 맞는지 확인해보자.**

- **그리고 views.py에서는**

```python
def post_detail_view(request, id):
    post = Post.objects.get(id=id)        # 게시글 1개에 대한 데이터 가져오기
    context = {
        'post': post,
    }

    return render(request, 'posts/post_detail.html', context)
```

- **이렇게 해당 id를 받을 수 있게 매개 변수를 설정했는지 확인하고 위의 url과 관련해서 post 인스턴스를 보내준 게 맞는지 확인해보자.**
