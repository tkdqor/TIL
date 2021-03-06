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


- **그리고 추가로, views의 PostListCreateView같은 경우, 우리가 이걸 작성했을 때 생성자를 넣어줘야 한다.** 생성자를 넣어주는 가장 간단한 방법은, 오버라이딩을 해주는 것이다.
  - **CreateAPIView를 command로 보면, post라는 메소드가 정의되어있다. 그리고 그 메소드는 create 메소드를 호출한다. 우린 이 코드를 불러와서 수정을 하는 것이다.** 
  - **그래서 일단 post 메소드를 가져온다. 그리고 create 메소드 코드도 가져와서 붙여넣어준다.**

```python
from rest_framework import status
...

# 게시글 목록 & 생성
class PostListCreateView(generics.ListAPIView, generics.CreateAPIView):
    queryset = Post.objects.all()
    serializer_class = PostListModelSerializer

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
    
    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        # self.perform_create(serializer)
        # 로그인 여부 판단해주기
        if request.user.is_authenticated:
            instance = serializer.save(writer=request.user)
            # instance.save()
        else:
            serializer.save()

        headers = self.get_success_headers(serializer.data)
        return Response(serializer.data, status=status.HTTP_201_CREATED, headers=headers)
```

- **이런식으로 가져와준다. status는 Import 해준다. 그래서 실질적으로 post, create 코드가 돌기 때문에 CreateAPIView가 작동하는 것이다. 그래서 post로 요청이 오면 그대로 create가 실행이 될 것이고, create가 실행이 되면 여기서 serializer를 가져오고, 유효성 검사도 하고 생성해준다.**
- self.perform_create(serializer)란 코드가 있는데 --> 이걸 command로 클릭해보면 perform_create라는 메소드가 serializer.save() 라는 코드를 실행시켜주고 있다.
  - 그래서 해당 부분을 주석처리하고 serializer.save()를 가져와줘도 된다. 
  - 그리고 다시 instance = serializer.save(writer=request.user) 이렇게 코드를 수정해주자. 
  - 이렇게 POST로 데이터를 생성할 때 작성자를 따로 받지 않았지만 --> serializer에서 save를 할 때 writer=request.user 이런식으로 값을 넣어주면 같이 save가 된다. 그래서 로그인된 유저가 자동으로 writer의 값이 된다. 따라서 로그인을 하지 않았을 경우에 POST로 날리면 오류가 발생하게 된다.
  - 여기에 추가로 로그인을 했으면 writer를 추가하고 / 로그인을 안 했으면 그냥 데이터를 저장하게끔 if문을 만들어줄 수 있다. 이렇게 수정하고 로그인을 하지 않을 때 POST로 생성해보면 writer는 null로 표시되고 데이터가 생성된다.



