## HTTP Method와 ViewSets (2)
- **urls.py에서 2개 패턴 잠깐 주석처리 해주고, router를 설정한 것을 다시 주석 해제하기.**

```python
# DRF routers 설정
router = routers.DefaultRouter()
router.register('posts', PostModelViewSet)        # Post 모델


urlpatterns = [
    path('', include(router.urls)),
    ...
]
```

- **이렇게 router 관련 코드를 살려주고 Views.py로 가서**

```python
...
# Post모델 ViewSet
class PostModelViewSet(ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostListModelSerializer
```

- **해당 viewset를 살려준다. 여기까지 설정하고 다시 서버를 실행해보면, http://localhost:8000/를 입력했을 때 Api Root가 잘 뜨는 것을 확인할 수 있다.**


### ModelViewSet
- 해당 클래스를 command로 들어가보면, 

```python
class ModelViewSet(mixins.CreateModelMixin,
                   mixins.RetrieveModelMixin,
                   mixins.UpdateModelMixin,
                   mixins.DestroyModelMixin,
                   mixins.ListModelMixin,
                   GenericViewSet):
    """
    A viewset that provides default `create()`, `retrieve()`, `update()`,
    `partial_update()`, `destroy()` and `list()` actions.
    """
    pass
```

- 이렇게 다양한 클래스들을 상속받고 있다. 생성/상세/수정/삭제/리스트 와 관련된 클래스이다. 여기서 CreateModelMixin와 ListModelMixin만 pk값이 안 들어가고 나머지는 다 pk값이 필요하다.
- **그리고 마지막에는 GenericViewSet이라는 클래스를 상속받는다.** 

```python
class GenericViewSet(ViewSetMixin, generics.GenericAPIView):
    """
    The GenericViewSet class does not provide any actions by default,
    but does include the base set of generic view behavior, such as
    the `get_object` and `get_queryset` methods.
    """
    pass
```

- **이 GenericViewSet은 ViewSet의 역할을 할 수 있게 해주는 기본적인 클래스이기 때문에 중요하다. 그래서 다른 클래스를 상속받아도 이 GenericViewSet을 상속받아야 ViewSet의 기능을 할 수 있다.**
  - 여기에서 ViewSetMixin은 as_view라는 메소드를 내부에 정의하고 있다. 그래서 우리가 urls.py에서 이 함수를 호출할 때 이것이 호출되는 것이다.
  - 그 다음 GenericAPIView는 실질적으로 API가 수행하기 위해서 필요한 queryset이나 serializer_class, get__queryset 이런것들이 정의되어 있다. 
  - **우리가 ModelViewSet이 상속받고 있는 ListModelMixin 클래스를 예로 들어보면, ListModelMixin을 command로 보면 -> ListAPIView가 호출되면 -> ListModelMixin이 호출이 될 것이고, list라는 메소드가 작동이 될 것이다. 그럼 그 안에 self.get_queryset이라는 게 호출이 된다. 그러면 그 때 -> GenericAPIView 안에 있는 get_queryset 메소드가 호출이 되는 것이다.**
  - queryset이라는 것을 우리가 views.py에서 queryset = Post.objects.all() 이렇게 정의를 해놓았다. 그래서 GenericAPIView에서 get_queryset은 이걸 가져오라는 의미이다. 
  - 그리고 if isinstance(queryset, QuerySet): 이렇게 QuerySet 클래스의 인스턴스가 맞는지 확인한다. 그 다음 이제 queryset를 응답해주는 게 GenericAPIView의 get_queryset 메소드이다.
  - 그러면 이제 ListModelMixin에서 self.get_queryset()에 해당 값이 채워지는 것이다. 그러면 self.filter_queryset()이 또 호출될 것이다.
  - 그러면 GenericAPIView에 filter_queryset 메소드가 호출된다. 백엔드에서 리스트로 쿼리셋을 가져오게 된다. 그래서 또 queryset를 반환해준다. 
  - 그래서 다시 ListModelMixin에 queryset 다음 코드가 실행이 된다. 페이지네이션 처리를 해준다.

- 10:50 -> 디버깅 관련 내용

- **이외에도 RetrieveModelMixin 클래스도 보면, self.get_object()라고 되어있다.** 
  - 이것도 GenericAPIView에 get_object라는 메소드로 정의되어있다. 또한 내부에 lookup_field도 pk를 의미하는 것이라서 들어와 있다. 그리고 마지막에 self.check_object_permissions(self.request, djb) 이 코드를 실행시키고 return obj로 object를 반환해준다.


- **이렇게 ViewSet이 우리가 1~2줄을 작성하지만, 내가 쓴 queryset과 serializer_class를 가지고 --> 정말 많은 코드들이 작동하고 django를 실행시키면서 내 서비스가 작동할 수 있게 해준다.**


### 관련 사이트
- https://www.cdrf.co/
- http://ccbv.co.uk/
- 우리가 위에 처럼 매번 다 들어가서 볼 수는 없다. http://ccbv.co.uk/projects/Django/4.0/django.views.generic.detail/DetailView/ 이렇게 들어가보면 여기에 있는 속성과 함수가 쭉 정리가 되어있다. 이걸 보면서 해당 클래스에 어떤 게 들어가는지 확인할 수 있다. 
- 그래서 django를 사용할 때 이 2개의 사이트를 꼭 참고하자.
- 그리고 이 속성들이 어디서부터 상속받았는지도 Defined in --> 이렇게 표시해준다.



### View 실습
- 우리의 VIew에서 PostModelViewSet 클래스를 수정해보자.

```python
from rest_framework.decorators import action
...

# Post모델 ViewSet
class PostModelViewSet(ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostListModelSerializer

    @action(detail=True, methods=['get'])
    def get_comment(self, request, pk=None):
        self.get_object()
```

- 여기서 먼저 action를 Import 해준다. 
- 그리고 action 데코레이터를 사용해서 코드를 추가해준다. 

26:14



