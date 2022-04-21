## Update로 데이터 수정하기 (2)
- 이제 views.py에서 post_update_view 함수를 수정해서 실질적으로 데이터가 수정되도록 설정해보자.

```python
def post_update_view(request, id):
    post = Post.objects.get(id=id)

    if request.method == 'GET':
        context = { 'post': post, }
        return render(request, 'posts/post_form.html', context)
    elif request.method == 'POST':
        image = request.FILES.get('image')
        content = request.POST.get('content')
        print(image)
        print(content)

        post.image = image
        post.content = content
        post.save()
        return redirect('posts:post-detail', post.id)
```

- **이렇게 elif 다음 요청 메소드가 POST일 경우, image와 content를 가져오고 위에서 가져온 post 인스턴스의 값들을 post.image = image / post.content = content 이렇게 바꿔주고
  post.save()로 저장해주면 된다.**
  - **이렇게 하면 데이터가 수정이 된다. 그런데, 수정할 때마다 다른 사진을 계속해서 업로드를 하게 되면 --> liongram 디렉터리 내부 media 디렉터리에 사진 파일들이 계속 추가가 되어버린다.** 
  - 그런데 사실 이러면 안 된다. 기존 이미지가 삭제되고 내가 입력한 게 추가로 올라가야 된다.

- **그래서, post_update_view를 다시 수정해준다.**

```python
def post_update_view(request, id):
    post = Post.objects.get(id=id)

    if request.method == 'GET':
        context = { 'post': post, }
        return render(request, 'posts/post_form.html', context)
    elif request.method == 'POST':
        new_image = request.FILES.get('image')
        content = request.POST.get('content')
        print(new_image)
        print(content)

        if new_image:
            post.image.delete()     # 기존의 이미지를 먼저 삭제
            post.image = new_image  # 그리고 새로운 이미지로 교체

        
        post.content = content
        post.save()
        return redirect('posts:post-detail', post.id)
```

- 만약, 새로운 이미지가 들어온다면 --> 새로운 이미지가 있을 때만 기존의 이미지를 새롭게 교체해주자고 if문을 설정한다.
  - **그리고 기존의 이미지를 post.image.delete() 이렇게 먼저 삭제해준 다음, post.image = new_image 이렇게 새로운 이미지로 교체를 해주면 --> 계속해서 사진이 무한정 늘어나지 않게 된다.**
