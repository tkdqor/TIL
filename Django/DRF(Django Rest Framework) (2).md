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


**2. 


