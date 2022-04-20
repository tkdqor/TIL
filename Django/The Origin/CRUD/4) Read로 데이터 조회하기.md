## Read로 데이터 조회하기
- 글 데이터를 list로 보여주기
- **views.py에 우리가 post_list_view라고 list를 만들기 위한 View를 설정했다. 여기서 코드를 입력하자.**

```python
...
def post_list_view(request):
    post_list = Post.objects.all()
    context = {
        'post_list': post_list,
    }

    return render(request, 'posts/post_list.html', context)
```

- 위의 코드처럼 Post 모델 데이터를 전부가져와서 변수를 넘겨주기.

- 그 다음은, post_list.html로 가서 
