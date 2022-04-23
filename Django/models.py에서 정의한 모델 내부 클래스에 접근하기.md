## models.py에서 정의한 모델 내부 클래스에 접근하기

```python
class Post(models.Model):

    class Category(models.TextChoices):     # 공지사항의 종류를 카테고리 클래스로 정의하기
        NORMAL = 'NORMAL', _('일반')
        DISABLED = 'DISABLED', _('장애인')
        ENVIRONMENT = 'ENVIRONMENT', _('환경')
        EMPLOYMENT = 'EMPLOYMENT', _('고용')
        EDUCATION = 'EDUCATION', _('교육')
        ELDERS = 'ELDERS', _('노인')

    author = models.ForeignKey(Customer, on_delete=models.CASCADE, related_name='author_post')
    title = models.CharField(max_length=100, default='None')
    category = models.CharField(max_length=20, choices=Category.choices, default=Category.NORMAL)   # 위에서 정의한 Category 클래스로 선택할 수 있게 설정
    body = models.TextField()  # 많은 양의 글자를 담기 위해 TextField 설정
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)   # 글 수정일자 자동으로 DB에 저장하기

    def __str__(self):
        return f'{self.title}'
```

- **위와 같이 Post 모델 내부에 Category라는 클래스가 정의되어 있을 때, 해당 Category 항목들을 template에 출력해보기.**


- template에 출력하기 위해 먼저 views.py를 보면,

```python
# 공지사항 게시판 생성하기
def board_create(request):
    posts_category = Post.Category.choices  # GET 방식으로 request 시, 카테고리 선택할 수 있도록 Post 모델의 Category 클래스에 접근해서 항목들을 보내주기
    context = {
        'posts_category': posts_category,
    }

return render(request, 'posts/board_create.html', context)
```

- **Post.Category.choices 이런식으로 Post 클래스 모델 내부에 정의된 Category 클래스에 접근하고 choices를 사용하면 된다.**

- 해당 변수를 template에서 for문으로 출력해보면,

```html
<div>
   <select name="category" id="board_category">
       <option disabled selected>카테고리 선택</option>
       {% for category in posts_category %}
       <option value="{{ category.0 }}">{{ category.1 }}</option>
       {% endfor %}
   </select>
</div>
```

- **그냥 1개를 뽑아서 category를 출력하면 튜플의 형태로 출력된다.**
  - **따라서 튜플의 첫번째 값인 DB값을 뽑으려면 category.0 / 튜플의 두번째 값을 뽑으려면 category.1 이런식으로 코드를 입력해주면 된다.**
