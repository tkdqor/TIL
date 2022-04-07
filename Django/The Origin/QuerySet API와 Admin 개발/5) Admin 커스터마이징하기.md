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
- ㅇㅇ
