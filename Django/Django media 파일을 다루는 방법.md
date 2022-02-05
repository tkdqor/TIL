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
- settings.MEDIA_ROOT 하위에 저장한 파일명 / 경로명 설정
- 디폴트는 파일명 그대로 settings.MEDIA_ROOT에 저장
- 성능을 위해서 한 디렉터리에 너무 많은 파일들이 저장되지 않도록 조정해야 함 / 동일 파일명으로 저장 시에, 파일명에 더미 문자열을 붙여 파일 덮어쓰기 방지

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
  - **장고는 실행 도중에 오류가 발생하면 DEBUG가 True인 경우 오류 내용을 화면에 상세하게 출력한다. 이때 settings.py 파일과 urls.py 파일에 설정한 항목이 모두 노출된다. 이 말은 파이보를 DEBUG=True 상태로 운영하면 오류 발생 시 서버 정보가 노출된다는 말과 같다. 이것은 어쩌면 서버 해킹 등의 매우 나쁜 결과를 초래할 수도 있다. 따라서 운영 환경에서는 반드시 DEBUG 항목을 False로 설정해야 한다.**
  - 이렇게 if문을 설정하는 이유는 -> django에서는 media나 static 파일 serving를 django 기본에서 실제 production에서 하는 것을 권장하지 않기 때문이다. 만약 if settings.DEBUG: 이 부분을 주석처리 한다고 해도, 실제 서비스에서는 DEBUG 옵션을 끄게 되므로, static 함수는 빈 리스트를 반환하게 된다.
  
16:36
