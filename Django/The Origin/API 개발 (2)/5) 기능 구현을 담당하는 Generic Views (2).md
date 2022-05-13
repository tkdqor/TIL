## 기능 구현을 담당하는 Generic Views (2)
- posts앱의 views.py에 추가해보기. 수정 기능 작성하기.
- django에서는 PUT과 PATCH가 다르기 때문에 수정 기능을 2가지로 만들어야 하지만,
  - 우리는 UpdateAPIView를 사용할 것이고 여기에서는 2가지가 모두 포함되어있다.

```python
# PUT/PATCH - 게시글 수정
class PostUpdateView(generics.UpdateAPIView):
    queryset = Post.objects.all()
    serializer_class = PostListModelSerializer
```

- 이렇게 설정해준다. 이제 urls.py에서 설정해주기
- 근데 문제가, path('posts/<int:pk>/', PostRetrieveView.as_view(), name='post-detail'), # Detail  이렇게 디테일과 관련된 url과 똑같해진다.

- 그래서 이 문제를 해결하기 위해 코드를 수정해주자. 먼저 views.py는

```python
# Read - 게시글 목록 & 생성
class PostListCreateView(generics.ListAPIView, generics.CreateAPIView):
    queryset = Post.objects.all()
    serializer_class = PostListModelSerializer



# Read - 게시글 상세 & 수정
class PostRetrieveUpdateView(generics.RetrieveAPIView, generics.UpdateAPIView):
    queryset = Post.objects.all()
    serializer_class = PostRetrieveModelSerializer
```

- 이렇게 게시글의 목록과 생성이 한 View에서 이루어지고 / 게시글의 상세와 수정도 한 View에서 이루어지게 한다. 또한 urls.py는

```python
urlpatterns = [
    # path('', include(router.urls)),
    path('admin/', admin.site.urls),

    # path('posts/', PostListView.as_view(), name='post-list'), 
    path('posts/', PostListCreateView.as_view(), name='post-list-create'),         # Read & Create
    path('posts/<int:pk>/', PostRetrieveUpdateView.as_view(), name='post-detail'), # Detail & Update 
 
    # 계산기 기능 / 함수 기반 API View
    # path('calculator/', calculator, name='calculator-fbv'),
    # 계산기 기능 / 클래스 기반 API View
    path('calculator/', CalculatorAPIView.as_view(), name='calculator-cbv'),
]
```

- 이런식으로 구성.

- **그 다음, http://localhost:8000/posts/1/ 이렇게 하나를 조회하면, 밑에 데이터를 입력하고 PUT를 누르면 수정도 할 수 있게 되어있다.**
  - 지금 DRF에서는 PUT만 나와있어서, postman을 활용해보자.
  - **Postman에서 http://localhost:8000/posts/3/ 이렇게 입력하고 PUT를 선택하고, Body - x-www-form-urlencoded 클릭해서 KEY에 content라고 입력하고 VALUE에는 다른 값들을 넣어보면 
    실제로 데이터가 수정된다.**


### PUT과 PATCH
- HTTP에서 PUT이랑 PATCH의 차이는, PUT은 자원의 전체를 교체하는 것이다. 그래서 사실 Postman에서 PUT으로 보낼 때 Body에 모든 필드가 들어가 있어야 한다. 그게 정상이다.
- 반면 PATCH 같은 경우에는 부분적으로 수정하는 것이다. 
  - **즉, PUT은 전체를 수정하는 것이고 PATCH는 입력받은 값만 수정하는 것이다.**

- **여기서는 content가 필수로 들어가야 하는 값이라서 PUT일 때는 content 없이 하면 오류가 발생한다. 다만, PATCH로는 수정이 가능하다.**

- **우리가 UpdateAPIView를 command로 클릭해보면, put과 patch 함수가 따로 정의되어있다. 그래서 업데이트를 한느 코드들이 작동된다.** 



### 게시글 삭제
- **게시글 삭제도 게시글 상세 & 수정 View에 같이 해주면 된다.** views.py를 보면,

```python

# 게시글 상세 & 수정 & 삭제
class PostRetrieveUpdateView(generics.RetrieveAPIView, generics.UpdateAPIView, generics.DestroyAPIView):
    queryset = Post.objects.all()
    serializer_class = PostRetrieveModelSerializer

```

- **이렇게 한 클래스에 3개를 상속받아 게시글 상세 & 수정 & 삭제가 모두 될 수 있도록 설정한다.**

- **우리가 이 API 서버 개발은 웹 개발과 조금 다르다. 왜냐면 URL이 겹치기 때문이다. 우리는 RESTful하게 작성하기 때문에 URL이 서로서로 똑같이 묶이게 된다.**

- Destroy 관련해서, DestroyAPIView를 계속 들어가면 DestroyModelMixin 이라는 클래스가 나오고, 여기에서는 삭제를 해준다. get_object라는 건, url의 id값을 통해 하나의 모델을 가져오는 것이다.
  - 즉, 모델에 있는 값을 인스턴스로 가져온다. 
  - **인스턴스는 : 특정 클래스에서 생성된 객체를 가지고 인스턴스라고 한다.**
  - **객체는 : 모든 클래스에서 만들어진 걸 객체라고 한다.** 

- 그래서 DestroyModelMixin에서 특정 인스턴스를 삭제해준다.

- **또한, 실험을 위해 Postman에서 url를 넣고 Delete로 Send를 하면 -> 아무것도 전달해주지 않고 204 상태 코드가 뜨기 때문에 정상적으로 삭제가 된다.**





