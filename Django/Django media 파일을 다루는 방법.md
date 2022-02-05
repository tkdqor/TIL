## Static & Media 파일
- **Static 파일**이란, 장고뿐만 아니라 다른 언어 / 프레임워크에도 존재하는 개념인데 개발 리소스로서 정적인 파일(js, css, image 등) 즉, 흔히 말하는 웹 프론트엔드 개발에서의 리소스들을 의미.
  - 앱이나 프로젝트 단위로 저장 / 서빙 


- **Media 파일**이란, 해당 용어는 장고에만 존재하는 용어이다. 
  - 장고 모델 필드 중에서 **FileField / ImageField**를 통해 저장한 모든 파일을 의미. 이 필드들은 클래스이기 때문에, 원한다면 pdf필드 혹은 엑셀필드와 같은 것들을 정의한 다음에 사용할 수도 있다.
  - DB 필드에는 저장경로를 저장하며, 파일은 파일 스토리지에 저장하게 된다. -> 실제로 문자열을 저장하는 필드이다.
  - 프로젝트 단위로 저장 / 서빙


### models.py에서 관련 필드 추가하기
```python
class Post(models.Model):
    message = models.TextField()           
    photo = models.ImageField(blank=True)    # 기본적으로 blank를 설정해줘야 migration이 진행됨
    is_public = models.BooleanField(default=False, verbose_name='공개여부')   
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
```

- 이렇게 ImageField를 추가하고 나서 우리가 pillow라는 라이브러리를 설치해줘야 한다. pillow는 python 이미징 라이브러리이다. 간단한 썸네일 만들기나 이미지에 대한 가로, 세로 크기 등을 조사하고자 할 때 유용하다.

```terminal
pip install pillow
```

- 그리고나서, manage.py가 있는 경로에(프로젝트 디렉터리) requirements.txt라는 파일을 만들자.
  - 해당 파일은, 현재 프로젝트에 사용되는 라이브러리들의 목록을 적어주는 것이다. 한 줄에 하나씩 나열하게 되면 나중에 한방에 설치할 때 편하다.

```terminal
pip install -r requirements.txt
```

- 이렇게 입력하게 되면, 한 줄에 하나씩 적어놓은 라이브러리들이 한 번에 설치를 진행하게 된다.
  - 적어줄 때, pillow는 버전을 탈 일이 별로 없지만, django는 버전을 적어주었다.

- 위에서 ImageField를 설정했으니, migration / migrate 진행하기. -> 그러면 이제 DB에 photo라는 필드가 생성된 것이다.
  - 그래서 admin 페이지에 데이터 하나를 클릭하면, 사진을 업로드 할 수 있는 photo 필드가 뜬다.


### Media 파일 처리 순서
1) HttpRequest.FILES라는 속성을 통해 파일이 전달
2) View 로직이나 Form 로직을 통해 파일에 대한 유효성 검증을 수행 (엑셀 파일인지, 이미지 파일이면 가로 크기가 어느정도인지..)
3) 유효성 검증에 통과한다면, FileField / ImageField에 실제 DB에 저장되는 것은 파일이 저장되는 경로(문자열)가 저장되고, 실제 파일들은 파일 스토리지(디렉터리)에 저장한다. 원한다면, 아마존S3, Azure 스토리지 등등의 클라우드 스토리지 상에서 저장을 할 수도 있다.
4) 기본 세팅으로는 settings.py -> MEDIA_ROOT 경로에 파일을 저장하게 된다.


### Media 파일 관련 settings 예시
- 보통 프로젝트를 시작하자마자 하는 설정 중에 하나이다.

- **MEDIA_URL = ""**
  - 각 media 파일에 대한 URL Prefix
  - 필드명.url 속성에 의해서 참조되는 설정

- **MEDIA_ROOT = ""**
  - 파일필드를 통한 저장 시에, 실제 파일을 저장할 ROOT 경로

- settings.py에 가서 STATIC_URL = '/static/' 이 코드 밑에다가
```python
import os

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

- **import os -> os라는 모듈을 import 하는 것. os 모듈은 Operating System의 약자로서 운영체제에서 제공되는 여러 기능을 파이썬에서 수행할 수 있게 해준다.**
  - 그리고 MEDIA_ROOT에서 os 모듈의 **os.path.join() 함수**를 사용하자. **os.path.join() 함수는 운영체제에 맞게 폴더 구분자를 다뤄서 경로를 생성해준다. 어떤 운영체제든지 경로를 알맞게 생성해서 에러를 발생하지 않게 해준다. 해당 함수 파라미터에 생성하고 싶은 경로의 문자열을 입력하면 경로를 만들 수 있다.**
  - os.path.join(BASE_DIR, 'media')는 -> BASE_DIR/media라는 URL이 생성된 것이다.

- 그리고 MEDIA_ROOT가 업로드된 파일이 저장되는 경로를 지정하는 것. BASE_DIR은 settings.py 위쪽에 값으로 정의되어 있는데, 여기서 던더file던더는 python 파일이 import 될 때 파일 경로를 담고 있다. 그래서

```python
BASE_DIR = Path(__file__).resolve().parent.parent   
# __file__가 현재 settings.py의 경로이고 여기서 resolve() 함수는 그 경로를 절대 경로로 만들어 준다. 그리고 parent가 2번이니까 상위, 상위의 위치를 의미한다.
```

- 이렇게 파일 경로를 절대경로로 바꿔주고 그 절대경로의 부모경로, 부모경로가 된다. **즉, settings.py의 절대경로의 부모의 부모이면 -> 현재 manage.py가 있는 프로젝트 디렉터리(프로젝트 루트)가 된다.**
  - 관련 블로그 https://wookkl.tistory.com/31

- 그래서 다시 아까 확인했던 **MEDIA_ROOT = os.path.join(BASE_DIR, 'media') 이 코드를 생각해보면, 루트 경로 밑에 있는 media 디렉터리에 업로드된 미디어 파일들을 저장하겠다는 설정이 된다.**
  - 만약, 이 설정이 안되어 있으면 프로젝트 루트에 바로 저장이 된다.
  - 혹은 다르게 상위 폴더를 만들어도 된다. 

```python
MEDIA_ROOT = os.path.join(BASE_DIR, '..', 'public', 'media')
```

- 경로에서 점 2개는 상위 폴더를 의미한다. 위의코드처럼 설정하면 프로젝트 경로의 한단계 올라간 디렉터리에서 public 폴더 밑에 있는 media 폴더에 저장되는 것이다.

* * *
## FileField와 ImageField
**1) FileField**
- File Storage API를 통해 파일을 저장.
- django에서는 File System Storage만 지원하고 django-storages를 통해 확장 지원한다.
- 해당 필드를 옵션 필드로 두고자 할 경우, null이 아닌 blank=True만 적용해도 된다.

**2) ImageField**
- Pillow(이미지 처리 라이브러리)를 통해 이미지 width / height 픽셀 크기 획득
  - Pillow 미설치 시에, ImageField를 추가한 makemigrations 수행에 실패하게 된다.

- 위 2개의 필드를 상속받아서 커스텀 필드를 만들 수도 있다. ex) PDFField / ExcelField 등


### 위의 필드와 관련해서 사용할 만한 필드 옵션
**1) blank 옵션**
- 업로드 옵션처리 여부 즉, 빈 경로를 허용할것이냐의 문제이다. 디폴트는 False이다.

**2) upload_to 옵션**
- settings.MEDIA_ROOT 경로의 하위 경로에 저장할 파일명 / 경로명 설정
- 디폴트는 파일명 그대로 settings.MEDIA_ROOT에 저장
- 성능을 위해서 한 디렉터리에 너무 많은 파일들이 저장되지 않도록 조정해야 함
- 동일 파일명으로 저장 시에, 파일명에 더미 문자열을 붙여 파일 덮어쓰기 방지 - 이것에 대한 검사를 django의 파일스토리지가 지원.

```python
class Post(models.Model):       
    message = models.TextField()    
    photo = models.ImageField(blank=True, upload_to='instagram/post/%Y/%m/%d') 
    is_public = models.BooleanField(default=False, verbose_name='공개여부')    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
```


- 이렇게 models.py에서 필드를 정의할 때 사용할 수 있다. 만약, 따로 지정하지 않게 되면 업로드한 이미지 파일이 우리가 지정한 media 폴더 안에 너무 많아지게 된다. 그래서 media 폴더 하위에 여러 개의 폴더를 구성하게 된다.
- 위의 코드처럼 instagram/post/%Y/%m/%d라는 이름을 주게 되면, 업로드한 사진들이 media 디렉터리 밑에 -> instagram 디렉터리 밑에 -> post 디렉터리 밑에 저장된다. 그리고 %Y%m%d는 날짜/시간에 대한 형식 지정자인데, 년도 4자리 / 월 2자리 / 일 2자리를 나타내준다. 그래서 이렇게 작성하면 해당 년월일로 이루어진 하나의 디렉터리가 post 디렉터리 밑에 생성이 되는 것이다.
  - 더 나아가서 photo = models.ImageField(blank=True, upload_to='instagram/post/%Y/%m/%d/%H/%M/%S')  --> 이렇게 작성하면 년/월/일/시/분/초 마다 디렉터리를 만들수도 있다. 

- 위와 같이 수정을 했다고 해서 이미 저장된 경로가 바로 바뀌는 건 아니다. 실제로 파일을 저장할 때 -> 반영이 되는 것이다. 그래서 이미 올린 이미지를 다시 올려보면,

<img src="https://user-images.githubusercontent.com/95380638/152632846-4512841e-d822-46c7-9a2e-61e61b0ce0cf.png" width="60%" height="60%">
<img src="https://user-images.githubusercontent.com/95380638/152632857-73f847d9-a64e-472e-a86b-15a25a891e6a.png" width="30%" height="30%">

- 이렇게 하위 폴더를 생성해서 늘어나는 이미지 파일들을 관리할 수 있다.

- 이미 다른 루트로 생성한 파일들에 대해서, ImageField나 FileField를 커스텀해서 삭제가 되도록 로직을 구성할 수도 있고, 별도의 배치성 명령을 만든 후 ImageField나 FileField에서 참조하는 목록을 구성해서 그 목록에 포함되어 있지 않은 파일들을 일괄적으로 삭제하는 배치 명령을 할 수도 있다.


**2-1) upload_to 인자**
- 파일 저장 시에 upload_to 함수를 호출하여 저장 경로를 계산 
  - 파일 저장 시에 upload_to 인자를 변경한다고 해서, DB에 저장된 경로값이 갱신되지는 않는다.

- upload_to의 인자는
  - 문자열로 지정하는 경우 : 파일을 저장할 '중간 디렉터리 경로'로서 활용
  - 함수로 지정하는 경우 : '중간 디렉터리 경로' 및 '파일명'까지 결정 가능 - 즉, 내가 올린 사진의 원본 이름이 아닌 특정한 이름으로 수정할 수 있다.


* * *

## 프로젝트 디렉터리 urls.py 설정
- 우리가 urls.py 내부에서 프로젝트 디렉터리 밑에 있는 settings.py를 참조를 하고 싶은데, 참조를 하기 위해서 settings를 import 해야하는데..

```python
from django.conf import global_settings
from askcompany import settings
```

- from askcompany import settings 이렇게 할 수 있지만, 이렇게 하면 절대 안된다. 왜냐하면 settings는 from django.conf import global_settings 이렇게 여기에 있는 global_settings와 settings 이 2가지가 합쳐져야 하기 때문이다. 기본 global_settings에다가 우리가 만든 settings를 overwirte하는 것이다. 그래서 이렇게 2줄로 표현하는 것이 아니고,

```python
from django.conf import settings

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog1/', include('blog1.urls')),   # 새롭게 url를 만들어주고 해당 url은 blog1 앱의 urls.py로 보내주기
    path('instagram/', include('instagram.urls')),  # 새롭게 url를 만들어주고 해당 url은 instagram 앱의 urls.py로 보내주기
]

settings.MEDIA_URL ~~
settings.MEDIA_ROOT ~~
```

- from django.conf import settings 이렇게 입력해주면 위의 2가지를 합쳐주게 된다. 그리고 urls.py에서 settings.py에 접근할 때는 settings.MEDIA_ROOT ~~ 이런식으로 접근하면 된다.


## 이미지 파일 업로드 해보기
- admin 페이지에서 사진 하나를 업로드하고 save 하기
- **그리고 나서 우리가 지정한 프로젝트 디렉터리 내부를 보면 -> 우리가 설정한 경로안에 사진이 저장되어 있다. 즉, MEDIA_ROOT가 사용된 것이다.**

- **MEDIA_URL의 경우는 -> 파일에 대한 URL 접근 시 사용된다. 즉, 파일의 URL를 통해 접근할 때 사용된다는 것.**
  - 어떠한 요청이 django로 왔을 때 /media/ 요청이 온다면 보여준다는 것이다. admin 페이지에 업로드한 사진 우클릭해서 '새 탭에서 링크 열기'를 해보면 우리가 설정한 URL로 접근은 되는데 오류가 난다.
  - **URL로 접근할 때 파일을 읽어서 응답해줘야 하는데, 그 설정을 urls.py에서 settings를 접근하여 코드를 입력함으로서 할 수 있다.**


```python
from django.conf.urls.static import static  # static 함수 import
from django.contrib import admin
from django.urls import path, include      # include를 새롭게 추가
from django.conf import settings

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog1/', include('blog1.urls')),   # 새롭게 url를 만들어주고 해당 url은 blog1 앱의 urls.py로 보내주기
    path('instagram/', include('instagram.urls')),  # 새롭게 url를 만들어주고 해당 url은 instagram 앱의 urls.py로 보내주기
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

- **static 관련 import는 static이라는 함수를 import 한 것이다.**
- static 함수의 첫번째 인자로 settings.py에서 정한 MEDIA_URL를 전달하고 / 두번째 인자로 document_root로 파일이 저장되는 경로를 넣어주면 된다.
- static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) -> 이 코드를 입력하면 URL 리스트를 주게 되므로 urlpatterns라는 리스트에 추가를 하면 된다.
  - urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 이런식으로 추가한다.  
  - 리스트 사이에서 + 기호는 2개의 리스트를 합치는 기능을 한다. https://wikidocs.net/14#_6

- 그리고 static 설정을 언제해줄 것인지 정해야 한다. django에 DEBUG라는 옵션이 있는데, 이 옵션이 참일 때만 static를 urlpatterns에 추가하는 것으로 if문을 설정한다.
  - 실제로 settings.py에 보면, DEBUG = True 이렇게 설정되어있다.
  - 이 DEBUG는 개발 모드일 때만 DEBUG이고 실제 서비스를 할 때는 False로 두어야 한다. 
  - **장고는 실행 도중에 오류가 발생하면 DEBUG가 True인 경우 오류 내용을 화면에 상세하게 출력한다. 이때 settings.py 파일과 urls.py 파일에 설정한 항목이 모두 노출된다. 이 말은 DEBUG=True 상태로 운영하면 오류 발생 시 서버 정보가 노출된다는 말과 같다. 이것은 어쩌면 서버 해킹 등의 매우 나쁜 결과를 초래할 수도 있다. 따라서 운영 환경에서는 반드시 DEBUG 항목을 False로 설정해야 한다.**
  - 이렇게 if문을 설정하는 이유는 -> django에서는 media나 static 파일 serving를 django 기본에서 실제 production에서 하는 것을 권장하지 않기 때문이다. 만약 if settings.DEBUG: 이 부분을 주석처리 한다고 해도, 실제 서비스에서는 DEBUG 옵션을 끄게 되므로, static 함수는 빈 리스트를 반환하게 된다.
  
- 이렇게 코드를 입력하고나서 admin 페이지에서 다시 사진을 우클릭으로 보면 이제는 화면에 뜬다.

* * * 

### instagram app -> admin.py 수정
- 우리가 이미 admin 모듈의 ModelAdmin 클래스를 상속받아 Post 모델을 등록했고, 내부 속성 list_display 리스트에 
```python
@admin.register(Post)             # wrapping
class PostAdmin(admin.ModelAdmin):
    list_display = ['id', 'photo_tag', 'message', 'message_length', 'is_public', 'created_at', 'updated_at']
    list_display_links = ['message']
    list_filter = ['created_at', 'is_public']
    search_fields = ['message']

    def photo_tag(self, post):                        # 위의 display에 photo_tag를 사용하기 위해 새롭게 정의하는 함수. 
        if post.photo:                                # 만약 Post 모델의 photo 필드의 문자열이 있다면(저장된 이미지가 있다면)
            return f'<img src="{post.photo.url}" />'  # 해당 이미지의 url를 얻을 수 있게 설정하고 return
        return None  


    # 이 함수는 post 모델의 객체가 넘어오는 것이다. 이건 admin이 알아서 호출해주는 것이다.
    def message_length(self, post):
        return f"{len(post.message)} 글자"
```

- 다음과 같이 'photo_tag'를 넣어주기 위해 밑에다가 photo_tag 라는 함수를 새롭게 설정했다.
  - 해당 함수는 만약 Post 모델의 photo 필드의 문자열이 있다면(저장된 이미지가 있다면) 해당 이미지의 url를 얻을 수 있게 설정한 것이다. 이렇게 저장하고 admin 페이지를 새로고침 해보면,

<img src="https://user-images.githubusercontent.com/95380638/152632068-b1d2a70a-d980-44c4-acfa-e050f46b3ebf.png" width="70%" height="70%">

- 이렇게 url를 보여준다. 그리고 장고는 태그를 바로 보여주지 않는다.

- 더 나아가서, 
```python
from django.utils.safestring import mark_safe

...

def photo_tag(self, post):                        # 위의 display에 photo_tag를 사용하기 위해 새롭게 정의하는 함수. 
        if post.photo:                                # 만약 Post 모델의 photo 필드의 문자열이 있다면(저장된 이미지가 있다면)
            return mark_safe(f'<img src="{post.photo.url}" style="width: 100px;" />')  # 해당 이미지의 url를 얻을 수 있게 설정하고 return
        return None    
```

- mark_safe를 import하고, 이렇게 mark_safe로 안전하다고 해줘야 admin 페이지에서 실제 사진으로 보여준다. 

<img src="https://user-images.githubusercontent.com/95380638/152632234-4c01e276-6e35-4d74-b76e-c97b1b5661ee.png" width="70%" height="70%">


* * *

## uuid를 통한 파일명 정하기
- 아래의 코드들을 만약 별도의 utils.py에 만들어둔다면, 어떤 모델에서든지 사용할 수 있다.

```python
import os 
from uuid import uuid4
from django.utils import timezone

def uuid_name_upload_to(instance, filename):
    app_label = instance.__class__._meta.app_label
    cls_name = instance.__class__.__name__.lower()
    ymd_path = timezone.now().strftime('%Y/%m/%d')
    uuid_name = uuid4().hex
    extension = os.path.splitext(filename)[-1].lower()
    return '/'.join([
        app_label,
        cls_name,
        ymd_path,
        uuid_name[:2],
        uuid_name + extension,
    ])
```

- 첫번째 인자로 instance가 넘어오고 두번째 인자가 업로드 된 파일명이 넘어온다.
- 그리고 instance.던더class던더 이렇게 하면 해당 모델의 클래스가 되고 해당 모델이 어떤 앱에 속했는지 읽어온다.
- cls_name = ~~ 여기 코드는 모델 클래스명에 대한 이름을 가져오고 소문자로 변경을 한다.
- 그리고 timezone를 통해서 업로드하는 현재 년월일을 가져오고 
- uuid_name = uuid4().hex 이렇게 uuid를 쓰면, 32글자 랜덤한 문자열을 얻을 수 있다.
- extension = os.path.splitext(filename)[-1].lower()는 -> 실제 파일명에서 split extension를 하면 2개의 항목 튜플이 나온다. 첫번째는 확장자를 제외한 이름과 두번째는 확장자이다. 지금은 [-1]이니까 확장자만 가져오게 되고, 소문자로 변경한 것이다.

* * * 
- 그리고 return에서 join를 해주게 되면, 우리가 랜덤한 경로를 가질 수 있게 된다.
- uuid_name[:2] -> uuid_name의 처음 2글자만 가지고 ([:2]이면 0,1을 의미) 폴더명을 구성하고, 
- uuid_name + extension -> 전체 32글자 + 파일 확장자가 된다.

- 이렇게 하면, app_label 디렉터리 밑에 -> cls_name 디렉터리 밑에 -> ymd_path 연월일 별도 디렉터리 밑에 -> uuid_name[:2] uuid 처음 2글자로 시작하는 디렉터리 밑에 -> uuid_name + extension 이러한 파일명으로 구성할 수 있게 된다.   



```terminal
python manage.py shell

>>> from uuid import uuid4 
>>> uuid4()
UUID('8267579c-4c0d-428a-8be7-7c8a8cad4d47')    

>>> uuid4().hex
'cf6661a5d6a2459aa5963d00fd10efd1'
```

- shell을 이용해서 보면 uuid는 하이픈을 제외한 32글자를 랜덤하게 만들어준다. 그리고 uuid4().hex라고 입력하면 32글자의 하이픈을 제외한, 랜덤한 문자열을 얻을 수 있다.

```terminal
>>> from os.path import splitext
>>> splitext('c:/hello/world/python.py')
('c:/hello/world/python', '.py')

>>> splitext('c:/hello/world/python.py.txt')
('c:/hello/world/python.py', '.txt')

>>> splitext('c:/hello/world/python')
('c:/hello/world/python', '')

>>> splitext('c:/hello/world/python.py.txt')[1]
'.txt'

>>> splitext('c:/hello/world/python.py.txt')[-1]
'.txt'
```

- 이렇게 splitext를 사용하면 확장자를 기준으로 나눠준 것을 튜플의 형태로 돌려준다. 확장자가 아예 없는 경우에는, 빈 문자열을 주게 된다.
- 튜플에 [1]을 하게 되면, 두번째 인자를 선택하게 되고 [-1]은 제일 마지막이라는 의미로 똑같이 두번째 인자를 가져오게 된다.


* * *
## Template에서 media URL 처리 예시
- 필드의 .url 속성을 활용하자.

```html
<img src="{{ post.photo.url }}" %}" />

{% if post.photo %}
<img src="{{ post.photo.url }}" %}" />
{% endif %}
```

- 이렇게 내부적으로 settings.MEDIA_URL과 조합을 처리
- 필드에 저장된 경로에 없을 경우, url계산에 실패할 수 있으니 필드명 저장 유무를 if문으로 체크

* 파일 시스템 상의 절대경로가 필요하다면, .path 속성을 활용하자. - settings.MEDIA_ROOT와 조합


```terminal
python manage.py shell
>>> from instagram.models import Post
>>> Post.objects.all()
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>

>>> Post.objects.get(pk=3)
<Post: 세번째 메세지>

>>> Post.objects.get(pk=3).photo
<ImageFieldFile: instagram/post/2022/02/05/barella2.jpeg>

>>> Post.objects.get(pk=3).photo.url
'/media/instagram/post/2022/02/05/barella2.jpeg'

>>> Post.objects.get(pk=3).photo.path
'/Users/kimsangbaek/Desktop/likelion/env/askcompany/media/instagram/post/2022/02/05/barella2.jpeg'


>>> Post.objects.get(pk=2).photo
<ImageFieldFile: None>

>>> Post.objects.get(pk=2).photo.url
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "/Users/kimsangbaek/Desktop/likelion/env/lib/python3.9/site-packages/django/db/models/fields/files.py", line 63, in url
    self._require_file()
  File "/Users/kimsangbaek/Desktop/likelion/env/lib/python3.9/site-packages/django/db/models/fields/files.py", line 40, in _require_file
    raise ValueError("The '%s' attribute has no file associated with it." % self.field.name)
ValueError: The 'photo' attribute has no file associated with it.
```

- 지금 전체 Post 모델에서 세번째만 이미지가 있는데, .photo로 접근하면 실제로는 이미지필드 파일이지만, 문자열이 저장된 필드가 출력된다. 그리고 .url로 접근하면 url 경로가 나온다. .path는 실제 저장되어있는 파일 시스템의 절대 경로가 나온다.
- 그리고 이미지가 없는 2번 게시물에 .photo와 .photo.url를 해보면 ValueError가 발생한다.
  - 그래서 추후에 photo 필드에 대한 .url / .path와 같은 접근이 필요할 때에는 반드시 if문 로직을 넣어주자.
  
  
* * *
## 개발환경에서의 media 파일 서빙 
- static 파일과 다르게, 장고 개발서버에서 서빙 미지원
- 그래서 개발 편의성 목적으로 직접 서빙 Rule을 추가해서 가능해질 수 있다.

```python
from django.conf.urls.static import static
from django.conf import settings

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```


### File Upload Handler
- File Upload Handler가 있다. 파일 용량이 다양할 수 있는데 웹 서버단에서 파일 용량을 어느정도까지만 허용할지 설정을 할 수 있다. 만약, 제한이 없다면 어떤 악의적인 목적으로 수 기가바이트 짜리 파일을 업로드 하려고 할 수 있다. 그런걸 다 받아버리면 디스크가 순식간에 차버린다.

- 실제 파일 디스크에 저장하기전에, 유저가 업로드한 파일을 임시적으로 어디에 담아두느냐에 대한 내용
  - django는 파일크기가 2.5MB 이하일 경우 -> 메모리에 담아서 처리를 한다. (MemoryFileUploadHandler)
  - 파일크기가 2.5MB 초과일 경우 -> 디스크에 담겨서 전달한다. (TemporaryFileUploadHandler)

- 관련 설정 : settings.FILE_UPLOAD_MAX_MEMORY_SIZE -> 2.5MB가 디폴트로 설정되어 있다.
