## 'ManyRelatedManager' object is not iterable 오류
- ManytoMany 관계로 설정한 모델의 데이터를 가져올 경우, all()를 꼭 붙여서 가져오자.

- Views.py에서,
```python
# 마이페이지
def mypage(request):

    profile = request.user.profile   # 로그인된 유저의 Profile 모델에 접근

    wishlists = request.user.liked_travels.all()  # 로그인된 유저가 좋아요를 누른 포토스팟 목록

    context = {
        'profile': profile,
        'wishlists': wishlists,
    }

    return render(request, 'accounts/mypage.html', context)
```

- html template은,
```html
{% for wishlist in wishlists %}
<p>위시리스트 : {{ wishlist.title }}</p>
{% endfor %}
```

- **위의 코드처럼, User 모델과 Travel 모델이 M:N관계로 설정되어 있을 때 -> 로그인된 유저가 좋아요를 누른 Travel 목록을 가져오려면, wishlists = request.user.liked_travels.all() 이렇게 all()를
  붙여서 가져와줘야 한다.(여기서 liked_travels는 related_name으로 설정)**
