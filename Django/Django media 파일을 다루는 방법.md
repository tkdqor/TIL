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

- 이렇게 설정해주면 된다. MEDIA_ROOT가 업로드된 파일이 저장되는 경로를 지정하는 것. BASE_DIR은 settings.py 위쪽에 값으로 정의되어 있는데, 여기서 던더file던더는 python 파일이 import 될 때 파일 경로를 담고 있다. 그래서

```python
BASE_DIR = Path(__file__).resolve().parent.parent
```

- 이렇게 파일 경로를 절대경로로 바꿔주고 그 절대경로의 부모경로, 부모경로가 된다. **즉, settings.py의 절대경로의 부모의 부모이면 -> 현재 manage.py가 있는 프로젝트 디렉터리(프로젝트 루트)가 된다.**

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
1) blank 옵션
- 업로드 
