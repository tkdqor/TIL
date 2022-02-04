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


5:29
