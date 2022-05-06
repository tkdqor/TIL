## DRF 설치 및 기본 설정 구성하기
- 프로젝트를 하나 생성하고 시작. [해당 과정](https://github.com/tkdqor/TIL/blob/main/Django/The%20Origin/Prologue/Django%20%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0.md) 참고해서 진행하기.
- **그래서 Basic 폴더 내부에 liongram-api라는 새로운 프로젝트가 존재할 수 있도록 설정.**
  - **우리가 프로젝트를 새롭게 생성할 때 config라고 하는 이유는, 설정 파일들을 모아놓는 디렉터리라고 설정하기 위해서이다.**


## Django REST Framework 설치하기
- 새로 만든 liongram-api에 들어와있는 상태에서 DRF를 설치해보자.
- https://www.django-rest-framework.org/tutorial/quickstart/ 해당 공식문서에 나와있는 과정이다. 

```terminal
pip install djangorestframework 

pip3 install djangorestframework
```

- 먼저 설치를 해준다.(나는 pip3로 설치해야 오류가 나지 않았다.) 이 상태에서 pip list를 해보면 DRF가 추가되었다. 그리고나서 settings.py에 추가를 해줘야 한다.

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

- 즉, DRF도 외부 패키지라고 보면 된다. 


- **지금 가상환경이 어떤 경로인지 확인하려면, VSCode 메뉴에서 View - Command Palette - Python Select Interpreter - venv에 들어가서 python 선택.**

* * *
- 그리고 liongram-api 내부안에 새로운 App인 posts를 만들어준다. 

```python
django-admin startapp posts
```

### Serializer 만들기
- models.py를 키고 우리가 liongram의 posts App에서 했던 모델 코드들을 그대로 가져와보자.
- **그리고 posts라는 App안에다가 serializers.py라는 파일을 만들어준다. 그리고 해당 파일 안에다가 다음과 같은 코드를 추가해준다.**

```python
from rest_framework.serializers import ModelSerializer
from .models import Post

class PostModelSerializer(ModelSerializer):
    class Meta:
        model = Post
        fields = '__all__'
 ```
 
 - **이렇게 ModelSerializer를 상속받아서 Post와 관련된 Serializer 클래스를 정의한다.**

- **그 다음에는 View이다.**

```python
from django.shortcuts import render
from rest_framework.viewsets import ModelViewSet
from .models import Post
from .serializers import PostModelSerializer


class PostModelViewSet(ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostModelSerializer
```

- **이렇게 ModelViewSet를 상속받아서 PostModelViewSet 클래스를 정의해주고, PostModelSerializer를 설정해준다.**


- **그 다음은 urls.py로 가서 추가한다.**

```python
from django.contrib import admin
from django.urls import path, include
from rest_framework import routers
from posts.views import PostModelViewSet

# DRF routers 설정
router = routers.DefaultRouter()
router.register('posts', PostModelViewSet)

urlpatterns = [
    path('', include(router.urls)),
    path('admin/', admin.site.urls),
]
```

- **router를 설정해주고 PostModelViewSet를 등록한다.**
- 그리고 router의 urls를 ''이 경로에 포함시키는 건데, posts가 그대로 들어가게 된다.
- 그래서 지금 상태에서 http://localhost:8000/ 해당 경로로 들어가보면, 

<img width="1160" alt="image" src="https://user-images.githubusercontent.com/95380638/167183943-631a02e5-05d4-4d2d-8a7c-5ad9eeae2ade.png">

- **다음과 같이 DRF화면이 뜨게 된다. 지금 우리가 ROOT로 API를 날렸는데, posts라는 리소스가 있고 url를 알려주고 있는 것이다.**

- 그래서, http://localhost:8000/posts/로 API를 날려보면,

<img width="1218" alt="image" src="https://user-images.githubusercontent.com/95380638/167185260-5b616699-0371-454e-bada-6803b665beaf.png">

- **이렇게 Post 모델에 대한 리스트 화면을 출력해준다. 이런식으로 url로 요청하면 GET방식이다.**
  - 해당 화면에서 **Content-Type**은 application/json, 즉 JSON 형태라는 것을 알려준다.
  - 그리고 **우측 밑에 POST라는 버튼**은, 데이터를 입력하고 새롭게 데이터를 생성할 수 있는 버튼이다. 실제로 데이터를 넣고 POST를 눌러보면

<img width="1080" alt="image" src="https://user-images.githubusercontent.com/95380638/167187212-67177497-d119-48f7-a4f9-433997d268e7.png">

- **이렇게 posts/로 API를 날릴 때, Post 모델 데이터를 JSON 형태로 볼 수 있게 된다.**

- **그리고 우리가 prefix 다음에 url_path를 넣게 되면 detail를 볼 수 있다고 배웠으니까, http://localhost:8000/posts/1 --> 이렇게 API를 날리면**

<img width="1116" alt="image" src="https://user-images.githubusercontent.com/95380638/167188554-725212ab-d5af-4674-91d6-ff2d361fc0b4.png">

- **이러한 디테일 화면을 보여준다. 그리고 우측 하단에 PUT이라는 버튼으로 기존 데이터를 수정할 수 있다.**






