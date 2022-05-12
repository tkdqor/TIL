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


* * *
- admin.py에 Post 모델 추가

```python
from django.contrib import admin
from .models import Post


# Register your models here.
@admin.register(Post)
class PostModelAdmin(admin.ModelAdmin):
    pass
```

- 그리고 admin 페이지 들어가서 Post 모델 데이터의 작성자를 설정해주기. 그리고 DRF를 보면,

<img width="538" alt="image" src="https://user-images.githubusercontent.com/95380638/168135042-efa4b57b-c46c-4613-b695-277a97a2482c.png">

- **이렇게 writer에는 아이디 값만 뜨게 된다.**


- **그래서 serializers.py에 있는 PostListModelSerializer 클래스 내부에다가 설정을 추가하자.**

```python
...

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
        depth = 1
```

- 이렇게 내부에 depth = 1를 넣어주면, 

<img width="946" alt="image" src="https://user-images.githubusercontent.com/95380638/168135606-48869dde-3e48-43f7-8875-a7b731ee6275.png">

- **이런식으로 원래는 user의 id값만 나왔던게 지금은 User 모델의 데이터를 다 불러오게 된다.**
  - ex) 게시글의 댓글을 가져올 때도 이렇게 설정하면 훨씬 더 편리할 것이다. 


