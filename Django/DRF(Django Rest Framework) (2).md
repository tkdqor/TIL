## DRF 기반의 CRUD API 서버 만들기

**1. app 내부 디렉토리에 serializers.py 생성 및 설정 이후, views.py 설정**
```python
from rest_framework import viewsets  # viewset import
from .serializers import PostSerializer  # 생성한 serializer import
from .models import Post  # app의 모델 import

class PostViewSet(viewsets.ModelViewSet):    # ModelViewSet 활용하기
    queryset = Post.objects.all()
    serializer_class = PostSerializer
```
- ModelViewSet을 활용하면 기본적인 CRUD 기능을 API 서버에서 구현할 수 있게 된다.
- 그리고 serializer_class를 설정해 이전에 생성한 PostSerializer를 사용하는 것이다.
- APIView 경우 CRUD 로직을 각각 작성해야 하지만 -> Viewset은 반복되는 로직을 하나의 클래스로 정의해서 queryset를 한번만 설정하면 된다. 그리고 Router를 사용해서 URL설정을 따로 할 필요가 없다.


**2. app 내부 디렉토리에 urls.py 설정**
```python
from django.urls import path, include
from rest_framework import routers  # router import
from . import views  # views.py import


router = routers.DefaultRouter()  # DefaultRouter 설정
router.register('viewset', views.PostViewSet)  # ViewSet과 함께 본인이 설정하고 싶은 router 등록 ex) ~~/viewset

urlpatterns = [
    path('', include(router.urls)),
]
```
- DefaultRouter를 설정하고 본인이 설정하고 싶은 router를 router.register()에서 설정하고 이전에 생성한 PostViewSet를 같이 등록해준다.
- 그리고 urlpatterns에 router의 url를 path로 추가해주자.


**3. runserver 후 해당 url로 접속**

**<img src="https://user-images.githubusercontent.com/95380638/149645063-ac72cf4d-1bd8-458f-937e-65460ba9952d.png" width="70%" height="70%">**
- 해당 url로 접속하게 되면 데이터베이스에 저장된 모든 데이터를 JSON 형태로 확인할 수 있다. (READ에 해당)


**<img src="https://user-images.githubusercontent.com/95380638/149645155-7f4d70fb-0c73-4c3c-b274-82161afb77d7.png" width="70%" height="70%">**
- 그리고 해당 페이지에서 밑으로 내려보면, 값을 입력할 수 있는 form이 생성된 것을 확인할 수 있다. form에서 값을 입력하고 POST를 누르면 새롭게 데이터베이스에 값이 저장된다. (CREATE에 해당)


**<img src="https://user-images.githubusercontent.com/95380638/149645282-9382650f-152c-4157-8ace-b083352171e9.png" width="70%" height="70%">**
- 접속한 url에서 추가로 model의 id값을 입력하게 되면, id값에 해당하는 데이터 1개에 대한 정보가 조회되고 밑의 form에서 수정할 값을 입력하고 PUT를 누르면 데이터가 수정이 되고 DELETE를 누르면 해당 데이터를 삭제할 수 있다.  (PUT, DELETE에 해당)


                                                                                                                                     
                                                                                                                                     




      


