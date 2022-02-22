## Media
- 이미지 업로드 기능을 위한 django Media
- client가 server에 이미지를 전송하고 -> django는 이미지를 어디에 저장할지 그 경로를 MEDIA_ROOT라는 변수를 통해 설정한다.
  - 그리고 그렇게 저장한 이미지를 그대로 전달하거나, html img element에서 사용할 때 이미지를 사용할 수 있게끔 하는 url이 필요하다.

- 그래서, settings.py에서 MEDIA_ROOT와 MEDIA_URL를 변수를 추가해서 설정해야 한다.
```python
# Media File이 저장될 경로
MEDIA_ROOT = BASE_DIR / 'media'

# MEDIA_ROOT에 저장된 File이 제공될 base URL
# 반드시 마지막이 / 로 끝나야 한다
MEDIA_URL = '/media/'
```

- **MEDIA_ROOT는 파일이 저장될 경로를 설정한다. 위의 코드는 -> 프로젝트 루트 디렉터리/media/이미지파일명 을 의미한다.**
- **MEDIA_URL은 해당 파일이 실제로 사용될 때의 url을 결정해준다. 위의 코드는 -> localhost:8000/media/이미지파일명 을 의미한다.**

* * *
- **그리고, 사용자가 업로드한 이미지를 기존 모델과 연동하기 위해서는 models.py에서 ImageField라는 것을 사용해서 새로운 어트리뷰트(필드)를 만들어주면 된다.**
  - ImageField 함수를 사용해서 첫번째 인자로 upload_to='posts' 라고 설정할 수 있다. upload_to는 어떤 경로에 이미지를 저장할지 MEDIA_ROOT를 기준으로 하위 경로를 지정할 수 있게 해준다. 따라서 지금은 
    MEDIA_ROOT/posts/ 즉, BASE_DIR/media/posts/ 라는 경로에 이미지파일이 저장된다.
  - 두번째 인자로 null=True로 지정해서 이미지를 추가하지 않더라도 게시물을 작성할 수 있게 설정할 수 있다.

```python
class Post(models.Model):
    
    user = models.ForeignKey(User, on_delete=models.CASCADE, null=False)   # User모델과 1:N관계 설정 / user 데이터가 삭제되면 게시글도 삭제
    title = models.CharField(max_length=50, null=True)
    body = models.TextField()
    image = models.ImageField(upload_to='posts', null=True)
    created_at = models.DateTimeField(auto_now_add=True)
    liked_users = models.ManyToManyField(User, related_name='liked_posts') # User모델과 M:N관계 설정 
```    

- 위와 같이 models.py에 이미지파일을 업로드 할 수 있게 설정해보자.
- 또한, 이 ImageField를 사용하기 위해서는 pillow라는 라이브러리를 사용해야 한다. 그래서 별도로 설치해줘야 한다.
```terminal
python -m pip install Pillow
```

- 설치 이후, migrations와 migrate를 진행하기.

- 이렇게 모델을 설정한 다음, 이제 프로젝트 디렉터리 내부 urls.py로 가서 설정을 해야 한다.
```python
...
from django.conf import settings    # 이미지 파일 업로드를 위한 settings import
from django.conf.urls.static import static  # 이미지 파일 업로드를 위한 static import

urlpatterns = [
    # admin 페이지 url
    path('admin/', admin.site.urls),
    # host 주소로 요청 시 posts앱의 main 페이지로 연결
    path('', views.main, name='main'),
    # posts/로 요청 시 posts 앱으로 연결
    path('posts/', include('posts.urls')),
    # accounts로 요청 시 accounts 앱으로 연결
    path('accounts/', include('accounts.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)  
  # 이미지 serving를 위해 settings에서 설정한 MEDIA_URL로 요청이 들어올 경우, MEDIA_ROOT 내부에서 검색 후 HTTP Response로 응답
```

- **django development 모드에서는 사용자가 업로드한 이미지가 저장되어 있어도, 자동으로 이를 연동해서 활용할 수가 없다. 그 이유는, 일반적으로 이러한 사용자가 업로드한 파일은 django가 직접 처리하지 않고 별도로
  web server라는 프로그램을 추가로 사용해서 해당 파일을 serving 하게끔 한다. 그런데 지금 우리는 web server라는 프로그램을 사용하고 있지 않고 오직 django server만 구동하고 있기 때문에, 
  development 모드의 django 서버에서 임시적으로 이러한 media 파일들을 serving 할 수 있게끔 해주는 코드를 추가하는 것이다.**
  - 해당 코드는 development 모드의 django server에만 유의미한 코드이다. 
  - 우리가 프로덕션 환경에서 실제 서비스를 배포하는 과정에서는 -> web server라는 별도의 프로그램을 추가로 사용하게 된다. 그래서 실제로 django server를 프로덕션 모드로 구동시킬 경우에는 이 코드가 
    동작하지 않게끔 내부적으로 구현되어 있다.
    
- + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) -> 해당 코드는 우리가 settings 모듈에 추가했던 MEDIA_URL에 해당하는 경로로 요청이 들어올 경우에, MEDIA_ROOT 내부에서
  해당 파일을 검색한 다음 파일이 있을 경우에 해당 파일을 HTTP Response로 응답해준다. 
  - 따라서, 별도의 View 함수를 실행하지 않고 여기서 모든 처리가 진행된다. 즉, 이미지를 바로 HTTP Response로 주게 된다. 그리고 이러한 기능은 추후에 서비스를 배포할 때는 web server라는 프로그램이 담당하게 된다.

- 위의 코드는 django 공식 문서에 작성 방식이 나와있다.

* * *

```html
<form method="POST" action="{% url 'posts:create' %}" enctype="multipart/form-data">
        {% csrf_token %}
  ...
  
 <!-- 이미지 업로드 코드 -->
        <div class="mb-3 col-md-6">
            <label class="form-label" for="image">이미지</label>
            <input type="file" class="form-control" id="image" name="image">
        </div>
...
```

- 게시글을 생성하는 new.html에서 이미지를 업로드 할 수 있게 코드를 추가하자. input element인데 type이 file이어야 한다.
- 그리고 form element에서 이미지나 파일을 업로드 할 경우에는 -> 반드시 메소드는 POST 방식으로, enctype이라는 attribute는 encoding-type의 약자이다. 여기에 "multipart/form-data"라는 값을 지정해줘야 한다.


- 이제, new template에서 이미지를 업로드하고 글 생성 버튼을 눌렀을 때, 실제로 데이터베이스에 추가해줘야 하니까 create View 함수를 수정하자.
```python
# 게시글 생성 기능
@login_required
def create(request):
    # if not request.user.is_authenticated:    # 로그인이 되지 않은 경우, login url로 redirect
    #     return redirect('accounts:login')

    user = request.user                      # 로그인된 유저 정보를 user라는 변수에다가 저장하기      
    body = request.POST.get('body')          # request.POST / 여기까지 딕셔너리의 형태이고 .get() 함수를 사용해서 안전하게 접근
    title = request.POST.get('title')
    image = request.FILES.get('image')

    post = Post(user=user, title=title, body=body, image=image)     # created_at 필드는 자동 설정 되어있음
    post.save()                               # Post(author=author, body=body, created_at=timezone.now()) timezone 모듈안에 now라는 함수   

    return redirect('posts:detail', pk=post.id)
```

- 우리가 이미지파일을 POST 방식으로 전송했다 하더라도 -> 파일의 경우에는 request.FILES라는 코드로 담겨서 전달이 된다. get 함수를 통해서 request.FILES 내부에 image라는 key로 전달된 데이터를 찾아서 image라는 변수에 저장한다. 만약 이미지가 전달되지 않았을 경우에는 None이 return 된다.(사용자가 이미지를 업로드하지 않고 글을 작성했을 경우)
- 그리고 데이터베이스에 추가할 때, post = Post(user=user, title=title, body=body, image=image) -> 이렇게 image 필드에 추가해준다.

* * *
- 이미지를 업로드하고 글을 작성한 이후에, detail 페이지에서 해당 이미지를 볼 수 있게끔 detail template를 수정.
```html
<h1>Post detail</h1>

    <!-- 이미지파일 보여주기 -->
    {% if post.image %}
        <div class="mb-3 col-md-6">
            <img class="img-fluid" src="{{ post.image.url }}" alt="Post Image">
        </div>
    {% endif %}    
...
```

- 해당 게시물에 연동된 이미지가 있을 경우에만 image element를 사용해서 이미지를 보여주면 된다.
  - **image element의 source attribute에다가 해당 이미지의 주소를 적어야 하는데 -> post.image.url의 형태로, post 객체의 image 필드에 접근하고 url를 붙여주면 된다.**
  - 만약, post.image라고 하게 되면 이미지에 대한 복합적인 정보가 다양하게 들어있다. post.image.width / post.image.height 등 너비나 높이를 추출해서 사용할 수 있다.

- 실제로 업로드를 해보면, 프로젝트 루트 기준으로 media라는 새로운 디렉터리가 생성된 것을 확인할 수 있다. MEDIA_ROOT 하위 경로로 posts까지 생성된 것을 볼 수 있다.


- 마지막으로 이미지파일을 수정할 수 있게 edit.html를 수정해보자.
```html
<h1>Edit Post</h1>

    <!-- 이미지파일 보여주기 -->
    {% if post.image %}
        <div class="mb-3 col-md-6">
            <img class="img-fluid" src="{{ post.image.url }}" alt="Post Image">
        </div>
    {% endif %}  

 <form method="POST" action="{% url 'posts:update' post.id %}" enctype="multipart/form-data">
        {% csrf_token %}

   ...
 <!-- 이미지 업로드 코드 -->
        <div class="mb-3 col-md-6">
            <label class="form-label" for="image">Image</label>
            <input type="file" class="form-control" id="image" name="image">
        </div>
```

- 이미지파일이 있으면 보여주고, 업로드를 할 수 있게 input element의 type을 file로 설정. form element에도 enctype="multipart/form-data" 추가.
- 그리고 수정 페이지에서 실제로 버튼을 눌렀을 때 수정이 되게끔 udpate View 함수를 수정.

```python
# 게시글 수정 기능
@login_required
def update(request, pk):
    try:
        post = Post.objects.get(id=pk, user=request.user)    # 수정해야할 post 객체 조회 / user 조건 추가
    except Post.DoesNotExist:                                # Post.DoesNotExist라는 에러에 대해서만 대응
        return redirect('posts:index')                       # 게시물을 못 찾으면 게시물 목록 페이지로 redirect

    # post.author = request.POST.get('author')    # post.author 값에 사용자로부터 POST 방식으로 입력받은 값으로 수정
    post.body = request.POST.get('body')        # post.body 값에 사용자로부터 POST 방식으로 입력받은 값으로 수정
    post.title = request.POST.get('title')
    image = request.FILES.get('image')
    if image:
        post.image = image
        
    post.save()

    return redirect('posts:detail', pk=post.id) # redirect 함수로 게시물 상세 페이지 보여주기
```

- **request.FILES.get('image') 이렇게 이미지를 전달받았을 경우에 image라는 변수에 해당 이미지 데이터가 채워진다. 만약 사용자가 이미지를 업로드 하지 않았다면 None이 저장된다.**
- **우리는 새로운 이미지가 추가될 때만 교체해주고 싶기 떄문에, image가 None이 아닌 경우에만 -> if image: 일 때만 post.image = image로 새로운 이미지로 기존 이미지를 대체.**
  - **만약, if문이 아닌 post.image = image라고 하게 되면 사용자가 파일 input에서 이미지를 선택하지 않은 경우에 -> server에 이미지 데이터가 전송되지 않는다. 그 상태에서 None이라는 값으로 덮어버리면 기존에 연동된 이미지가 날아가게 된다.**
