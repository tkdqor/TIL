## 데이터를 다루는 Serializers (2)
- 우리가 기존에 작성한 posts 앱 내부의 serializers.py를 보면 이미 작성해놓은 코드가 있다. ModelSerializer를 상속받아 설정했었다. 
  - 해당 프로젝트 서버를 구동시키면, 처음 화면으로 DefaultRouter 화면이 나오게 된다. 
  - **View에서는 PostModelViewSet을 설정해서 ModelViewSet를 상속했었다. 또한, serializers.py에 정의한 PostModelSerializer를 가져왔다.**
  - **url에서는 DefaultRouter를 설정해서 PostModelViewSet를 등록했다.**

- 우리는 지금은 Serializers를 다시 수정해보자. 기존에는

```python
class PostModelSerializer(ModelSerializer):
    class Meta:
        model = Post
        fields = '__all__'
        # fields = ['id', 'writer', 'content', ]
```

- 이렇게 작성을 했다면, 이번에는

```python
# 새롭게 코드 수정해보기
# Base가 항상 상위 클래스가 된다
class PostBaseModelSerializer(ModelSerializer):
    class Meta:
        model = Post
        fields = '__all__'


# PostBaseModelSerializer 상속받아서 설정
class PostListModelSerializer(PostBaseModelSerializer):
    pass
```

- 이런식으로 Base 클래스를 설정하고 그 클래스를 상속받아 PostListModelSerializer를 정의해준다. 그리고 urls.py에서는,

```python
from .serializers import PostModelSerializer, PostListModelSerializer
...

class PostModelViewSet(ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostListModelSerializer
```

- 이렇게 PostListModelSerializer를 설정해주면 된다.


4:47


