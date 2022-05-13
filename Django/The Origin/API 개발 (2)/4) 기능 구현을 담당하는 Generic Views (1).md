## 기능 구현을 담당하는 Generic Views (1)
- View는 크게 2가지로 구분된다. Generic Views와 ViewSet이다.


- **일단 posts 앱의 views.py에 코드를 추가해보자. 그중에서도 Read 부분을 작성하자.**

```python
from rest_framework import generics
...

# Read
class PostListView(generics.ListAPIView):
    queryset = Post.objects.all()
    serializer_class = PostListModelSerializer

```

- 여기서 generics를 command로 들어가보면 ListAPIView 함수가 내재되어 있다. 또 GenericAPIView도 클릭해보면 APIView를 항상 상속하고 있다. 
- PostListView는 ListAPIView를 상속받았는데, ListAPIView를 클릭해보면 이미 작성된 코드가 있다. 그리고 거기서 상속받은 ListModelMixin을 클릭해보면 list라는 함수를 정의해두었다. 그래서 특정 모델의 쿼리셋을 가져와준다. 그래서 페이지네이션이 필요하면 처리하고 응답해준다. 
  - 그래서 항상 queryset이 들어간다고 생각하면 된다. 
  - 그리고 항상 serializer가 필요하다.


### read 작성
- 여기까지 view를 작성했다면, 이제 urls.py를 수정한다. 
  - 이번에는 routers를 쓰지 않고 url 패턴에 추가해본다.


```python
urlpatterns = [
    # path('', include(router.urls)),
    path('admin/', admin.site.urls),

    path('posts/', PostListView.as_view(), ),

    # 계산기 기능 / 함수 기반 API View
    # path('calculator/', calculator, name='calculator-fbv'),
    # 계산기 기능 / 클래스 기반 API View
    path('calculator/', CalculatorAPIView.as_view(), name='calculator-cbv'),
]
```

- path('posts/', PostListView.as_view(), ), 이렇게 path를 사용해준다.  

- **그리고 http://localhost:8000/posts/ 여기로 들어가면 Post 모델의 리스트를 보여준다.** 즉, routers를 사용하지 않고 path를 사용해도 가능하다. 

* * *

### Detail 작성
- **그 다음은 datail 페이지이다.**
- **이것도 views.py에다가 만드는 것이다. 그 전에 serializers.py로 가서 새로운 클래스를 정의해준다.**

```python
# Post 디테일 보여주기
class PostRetrieveModelSerializer(PostBaseModelSerializer):
    class Meta(PostBaseModelSerializer.Meta):
        dpth = 1
```

- **그 다음으로 views.py로 간다.**

```python
from .serializers import PostBaseModelSerializer, PostListModelSerializer, CommentHyperlinkedModelSerializer, PostRetrieveModelSerializer
...

# Read - 게시글 상세
class PostRetrieveView(generics.RetrieveAPIView):
    queryset = Post.objects.all()
    serializer_class = PostRetrieveModelSerializer
```

- 여기서 RetrieveAPIView를 클릭해보면 RetrieveModelMixin도 클릭해보면 코드 내부에 get_object()를 호출하게 된다. 그래서 직렬화를 해주는 것이다. 

- 그리고 마지막으로 urls.py 설정을 해준다.

```python
from posts.views import PostListView, PostModelViewSet, calculator, CalculatorAPIView, CommentModelViewSet, PostRetrieveView
...

urlpatterns = [
    # path('', include(router.urls)),
    path('admin/', admin.site.urls),

    path('posts/', PostListView.as_view(), ),
    path('posts/{int:pk}/', )

    # 계산기 기능 / 함수 기반 API View
    # path('calculator/', calculator, name='calculator-fbv'),
    # 계산기 기능 / 클래스 기반 API View
    path('calculator/', CalculatorAPIView.as_view(), name='calculator-cbv'),
]
```


```
urlpatterns = [
    # path('', include(router.urls)),
    path('admin/', admin.site.urls),

    path('posts/', PostListView.as_view(), name='post-list'),
    path('posts/<int:pk>/', PostRetrieveView.as_view(), name='post-detail'),

    # 계산기 기능 / 함수 기반 API View
    # path('calculator/', calculator, name='calculator-fbv'),
    # 계산기 기능 / 클래스 기반 API View
    path('calculator/', CalculatorAPIView.as_view(), name='calculator-cbv'),
]
```

- 이렇게 path('posts/<int:pk>/ 이런식으로 알 수 없으면 요청해야 한디.

- **여기까지 설정했으면 http://localhost:8000/posts/1/ 이렇게 디테일 페이지로 출력이 되는 것을 확인했습니다.**


* * *

### 생성 추가하기
- 먼저 urls.py를 추가해준다.
- 일단, views.py에 가서 

```python
# Read - 게시글 목록 & 생성
class PostListView(generics.ListAPIView, generics.CreateAPIView):
    queryset = Post.objects.all()
    serializer_class = PostListModelSerializer
```

- 이렇게 설정하고 나머지는 수정할 필요가 없다. 


- **그리고 브라우저에서 http://localhost:8000/posts/ 로 들어와서 POST를 날려보자. POST로 날리면 데이터가 1개 생성된다.**

- 이렇게 작동하는 원리는, 처음에 CreateAPIView를 클릭해서 들어가보면, CreateModelMixin으로 들어갈 수 있고 GenericAPIView가 나온다. 
  - **그런데 CreateAPIView의 경우에는 내부 코드가 post 메소드로 되어있다. 반면에 ListAPIView는 get 메소드로 되어있다. --> 그래서 하나의 url로도 같이 할 수 있다.** 


- **그리고 class PostListView(generics.ListAPIView, generics.CreateAPIView): 이렇게 여러 개가 합쳐진게 Viewset이다.**



- **이렇게 기본적으로 사용하는 것들을 전부 클래스로 저장시켜놨기 때문에 이런 부분이 객체지향 언어인 것이다.**


