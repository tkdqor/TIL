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


```html
 <!-- 이미지 업로드 코드 -->
        <div class="mb-3 col-md-6">
            <label class="form-label" for="image">이미지</label>
            <input type="file" class="form-control" id="image" name="image">
        </div>

- ddd
