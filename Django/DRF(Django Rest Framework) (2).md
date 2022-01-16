## DRF 기반의 CRUD API 서버 만들기

**1. app 내부 디렉토리에 serializers.py 생성 및 설정 이후, views.py 설정**
```python
from rest_framework import viewsets  # viewset import
from .serializers import PostSerializer  # 생성한 serializer import
from .models import Post  # app의 모델 import

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
```

