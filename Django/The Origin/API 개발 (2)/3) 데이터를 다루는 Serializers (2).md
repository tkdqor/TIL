## 데이터를 다루는 Serializers (2)
- 우리가 기존에 작성한 posts 앱 내부의 serializers.py를 보면 이미 작성해놓은 코드가 있다. ModelSerializer를 상속받아 설정했었다. 
  - 해당 프로젝트 서버를 구동시키면, 처음 화면으로 DefaultRouter 화면이 나오게 된다. 
  - **View에서는 PostModelViewSet을 설정해서 ModelViewSet를 상속했었다. 또한, serializers.py에 정의한 PostModelSerializer를 가져왔다.**
  - **url에서는 DefaultRouter를 설정해서 PostModelViewSet를 등록했다.**

- **우리는 지금은 Serializers를 다시 수정해보자.** 기존에는

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
    class Meta(PostBaseModelSerializer.Meta):
        # 부모에서 모델과 필드가 있기 때문에 적지 않아도 됨
        fields = [
            'id', 
            'image',
            'created_at',
            'view_count',
            'writer',
        ]
        # exclude = ['content', ]
        
        
class PostCreateModelSerializer(PostBaseModelSerializer):
    class Meta(PostBaseModelSerializer.Meta):
        fields = [
            'image', 
            'content',
        ]


class PostDeleteModelSerializer(PostBaseModelSerializer):
    pass

```

- **이런식으로 Base 클래스를 설정하고 그 클래스를 상속받아 PostListModelSerializer를 정의해준다. fields에서 content만 빼고 새롭게 정의해줄 수 있다. http://localhost:8000/posts/ 해당 DRF를 보면 content가 빠져있다. 그리고 Create나 Delete 역할을 하는 클래스도 정의해줄 수 있다.**
  - 그래서 이렇게 기본적인 것들은 Base 클래스에 정의해놓고 추가로 다른 클래스에서 코드를 입력해주면 된다.


- 그리고 urls.py에서는,

```python
from .serializers import PostBaseModelSerializer, PostListModelSerializer
...

class PostModelViewSet(ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostListModelSerializer
```

- 이렇게 PostListModelSerializer를 설정해주면 된다.


4:47


