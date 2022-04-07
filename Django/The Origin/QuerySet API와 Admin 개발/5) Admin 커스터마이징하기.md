## Admin 커스터마이징하기
- admin 페이지를 원하는대로 구현해보기


### 모델 데이터 표시
- 기존에는 데이터 객체 하나를 Post object (6) 이렇게 표시하고 있음
- 이걸 수정하려면 admin.py로 가서 코드를 추가하자.
  - 우리가 클래스를 상속받아서 필드들을 커스텀해야된다. 그래서 별도로 클래스를 정의해주자.

```python
from django.contrib import admin
from .models import Post, Comment

# Register your models here.

# Post 모델 등록
@admin.register(Post)
class PostModelAdmin(admin.ModelAdmin):
    list_display = ('id', 'image', 'content', 'created_at', 'view_count', 'writer')
    list_editable = ['content', )
    list_filter = ('created_at', )
    search_fields = ('id', 'writer__username')
    search_help_text = '게시판 번호 및  작성자 검색이 가능합니다.'

```

- **해당 클래스는 admin App - ModelAdmin이라는 클래스를 상속받았고, 그 내부에 list_display라는 속성을 정의해준다. --> 그러면 어드민 페이지에서 데이터를 id와 content 필드에 맞게 나타내준다.**
  - 즉, list_display 속성에 해당 모델 필드를 설정해주면 필드에 맞게 데이터가 출력이 된다.

- 또 다른 속성은, **list_editable**이라는 속성인데 이걸 설정한 필드는 데이터를 보는 화면에서 바로 수정할 수 있도록 화면이 바뀐다. 그래서 데이터 1개씩 조회할 필요 없이 바로 수정하고 저장할 수 있다.**

- **list_filter**라는 속성도 있다. 그래서 특정 필드를 리스트한테 설정해주면 --> 그 필드 기준으로 필터가 생긴다.
  - list_editable이나 list_filter 모두 [ ] 이렇게 리스트 형태나 ('..', ) 이렇게 튜플 형태로 둘 다 상관없지만, 실제로 ModelAdmin 클래스를 command로 클릭해봤을 때 --> 해당 속성들이 
    튜플로 정의가 되어있기 때문에 튜플로 맞춰주는 것이 좋다. 튜플 형태에서 1개 밖에 없을 때는 ('...', ) 이렇게 콤마를 넣어줘야 한다.

- **search_fields** 는 설정한 필드값이 검색될 수 있도록 해주는 속성이다. 위와 같이 search_fields = ('id', ) 이렇게 필드를 정해준다.
  - writer 필드의 경우, search_fields = ('id', 'writer__username') 이렇게 해주면 된다.
    - **즉, Post 모델의 writer라는 필드는 ForeignKey로 설정이 되어있기 때문에 --> 1의 관계에 있는 User 모델의 필드를 설정해줘야 한다. 여기서는 User 모델의 username이라는 필드로 검색할 수
      있게끔 설정한 것이다.**

- **search_help_text** 는 이번에 새로 생긴 속성으로 검색창을 설명해주는 속성이다.

<img width="1155" alt="image" src="https://user-images.githubusercontent.com/95380638/162110135-3b4ed11a-0dde-41b7-9b8e-e729016d9b99.png">

- 이렇게 위와 같이 기본 커스팀을 통해 어드민 페이지를 구성해볼 수 있다. 또한, 공식 문서인 https://docs.djangoproject.com/en/4.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.search_fields 해당 페이지에 다른 속성들(폼을 수정한다던지)도 있으니 참고해보자.
    
    

- 참고로 **ModelAdmin 클래스**를 command로 클릭해보면,

```python
class ModelAdmin(BaseModelAdmin):
    """Encapsulate all admin options and functionality for a given model."""

    list_display = ("__str__",)
    list_display_links = ()
    list_filter = ()
    list_select_related = False
    list_per_page = 100
    list_max_show_all = 200
    list_editable = ()
    search_fields = ()
    search_help_text = None
    date_hierarchy = None
    save_as = False
    save_as_continue = True
    save_on_top = False
    paginator = Paginator
    preserve_filters = True
    inlines = []
    ...
```

- **이렇게 우리가 상속받은 다음, 어드민 페이지에서 사용해볼 수 있는 속성들이 나열된다. 참고해서 보면 좋다.**


* * * 
### 어드민 페이지에서 Post 모델 데이터 클릭 시 댓글 데이터도 연동시키기
- 일단 Comment 모델은 굳이 따로 어드민 페이지에서 표시할 필요가 없기 때문에 admin.py에서 제거해주자.
- **ModelAdmin에 정의되어있는 inlines 라는 속성을 PostModelAdmin 내부에 사용해보자.**
  - **그러기 위해서는 먼저 CommentInline이라는 클래스를 정의해줘야 한다.**

```python
from django.contrib import admin
from .models import Post, Comment

# Register your models here.

# 어드민 페이지에서 Post 모델과 같이 볼 수 있게 CommentInline 클래스 설정
class CommentInline(admin.TabularInline):
    model = Comment


# Post 모델 등록
@admin.register(Post)
class PostModelAdmin(admin.ModelAdmin):
    list_display = ('id', 'image', 'content', 'created_at', 'view_count', 'writer')
    # list_editable = ['content', )
    list_filter = ('created_at', )
    search_fields = ('id', 'writer__username')
    search_help_text = '게시판 번호 및  작성자 검색이 가능합니다.'
    inlines = [CommentInline]


# Comment 모델 등록
# admin.site.register(Comment)

```

- **CommentInline이라는 클래스를 정의하고, Post 모델 클래스 내부에서 inlines 라는 속성을 통해 CommentInline 클래스를 연결해주는 것이다.**
- 그러면 이렇게

<img width="1137" alt="image" src="https://user-images.githubusercontent.com/95380638/162113901-74c29708-6da4-441d-b93f-fc21784fdc14.png">

- Post 모델 데이터 클릭하면 내부에 댓글이 자동으로 연결되어 표시가 된다. 마찬가지로 https://docs.djangoproject.com/en/4.0/ref/contrib/admin/#django.contrib.admin.InlineModelAdmin 공식문서에 더 많은 옵션들이 있다.
- 아니면 코드를 통해 확인할 수도 있다.
  - 위의 코드 중 TabularInline 이라는 부분을 command로 클릭해보면 class TabularInline(InlineModelAdmin): 이렇게 나오고 여기서 InlineModelAdmin를 한번 더 command로 클릭하면

```python
class InlineModelAdmin(BaseModelAdmin):
    """
    Options for inline editing of ``model`` instances.

    Provide ``fk_name`` to specify the attribute name of the ``ForeignKey``
    from ``model`` to its parent. This is required if ``model`` has more than
    one ``ForeignKey`` to its parent.
    """

    model = None
    fk_name = None
    formset = BaseInlineFormSet
    extra = 3
    min_num = None
    max_num = None
    template = None
    verbose_name = None
    verbose_name_plural = None
    can_delete = True
    show_change_link = False
    checks_class = InlineModelAdminChecks
    classes = None
    ...
```

- **이렇게 InlineModelAdmin이 클래스로 정의되어있다.**

- admin.py에서 CommentInline만 다시 수정해보면,

```python
# 어드민 페이지에서 Post 모델과 같이 볼 수 있게 CommentInline 클래스 설정
class CommentInline(admin.TabularInline):
    model = Comment
    extra = 1
    min_num = 3
    max_num = 5
    verbose_name = '댓글'
    verbose_name_plural = '댓글'
```

- **extra**라는 속성을 사용해서 1로 주면, 기본적으로 모든 Post 데이터가 1개의 댓글창을 가지게 된다.
- 그리고 **min_num과 max_num**이 있다. min_num은 1개의 글에 최소 댓글이 3개가 있게끔 설정하는 것이다. 그래서 댓글을 삭제하다가 3개가 되면 더 이상 삭제할 수 없게끔 설정한다. 그리고 max_num도 5개라면 5개인 상태에서는 더 이상 댓글을 추가할 수 없게끔 설정해준다.
- **verbose_name**은 댓글은 나타내는 제목이 영어가 아닌 '댓글'로 수정된다. **verbose_name_plural**까지 설정해주면 댓글이 여러개일 때 복수형으로 댓글S 가 아니라, '댓글'이렇게 띄우게 된다.


```python
# 어드민 페이지에서 Post 모델과 같이 볼 수 있게 CommentInline 클래스 설정
class CommentInline(admin.StackedInline):
    model = Comment
    extra = 5
    min_num = 3
    max_num = 5
    verbose_name = '댓글'
    verbose_name_plural = '댓글'
```    



- **지금까지는 CommentInline 클래스가 TabularInline이라는 클래스를 상속받아 정의했는데, 이게 아니라 StackedInline을 상속받는 경우를 해보자.**
  - 이렇게 수정하면, post 데이터 내부에 댓글 표시 부분이 세로 방향 정렬 중심으로 변하게 된다. 
  - 우리가 직전에 했던 TabularInline 클래스는 가로 방향 정렬 중심으로 보여준다.


### Post 모델 필드 중, 작성일자 나타내기
- 기본적으로 우리가 Post 모델 필드들을 정의할 때, created_at 필드는 auto_now_add=True 속성을 주었기 때문에 자동으로 데이터가 기록된다. 그래서 어드민 페이지에서는 우리가 따로 볼 수 없다. 수정을 할 수 없게 막아져 있기 때문이다. 
- 이러한 필드는 "읽기 전용"이라고 해서 속성을 주면 볼 수 있다.

```python
# Post 모델 등록
@admin.register(Post)
class PostModelAdmin(admin.ModelAdmin):
    list_display = ('id', 'image', 'content', 'created_at', 'view_count', 'writer')
    # list_editable = ['content', )
    list_filter = ('created_at', )
    search_fields = ('id', 'writer__username')
    search_help_text = '게시판 번호 및  작성자 검색이 가능합니다.'
    readonly_fields = ('created_at', )
    inlines = [CommentInline]
```

- 이렇게 **readonly_fields = ('created_at', )** 라고 속성을 추가하고 post 데이터 1개씩 들어가보면 

<img width="780" alt="image" src="https://user-images.githubusercontent.com/95380638/162116261-62d2407d-7e40-46b2-ab09-64beebd173a9.png">

- 작성일이 추가된 것을 확인할 수 있다. 다만 수정은 할 수 없다.


### 어드민 페이지 액션 추가하기
<img width="868" alt="image" src="https://user-images.githubusercontent.com/95380638/162116669-a8044eb1-3363-49c3-a14d-24d2907f4c83.png">

- 지금은
