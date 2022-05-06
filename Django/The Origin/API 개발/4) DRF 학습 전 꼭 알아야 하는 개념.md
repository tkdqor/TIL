## DRF 학습 전 꼭 알아야 하는 개념
- https://www.django-rest-framework.org/ 이 DRF 공식 문서를 중심으로 확인해보기. 
- **여기서 먼저 API Guide - Requests를 클릭해보기.**
  - DRF에서는 "request.data"를 사용해서 요청된 데이터를 가져올 수 있다. 

- **API Guide - Responses를 클릭해볼 수 있다.**
  - django에서 응답할 때는 기본이 render를 많이 사용하게 된다. 즉, 기본이 html 응답이다.
  - 반면 DRF에서는 기본이 JSON 형태로 응답한다고 보면 된다. python의 딕셔너리 형태와 비슷하다. JSON은 JavaScript Object Notation의 약자이다. 

- **API Guide - Views를 클릭해보자.**  
  - 기본적으로 DRF는 Class-based Views를 사용한다. django에서는 CreateView, ListView 등 form과 관련된 View를 사용했지만, DRF에서는 APIView를 기본적으로 사용하게 된다. 
  - 그리고 @api_view() 같은 경우는 함수 View를 사용할 수 있도록 해준다.
  - 또한, API Guide - Generic Views을 클릭해보면, 여기서는 ListCreateAPIView 와 같은 View를 사용하기도 한다.

```python
from django.contrib.auth.models import User
from myapp.serializers import UserSerializer
from rest_framework import generics
from rest_framework.permissions import IsAdminUser

class UserList(generics.ListCreateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = [IsAdminUser]
```

- 해당 예시를 보면, 클래스 내부에 serializer_class라는 것이 있다. 
- **우리가 DRF는 응답을 JSON형태로 한다고 했는데, 예를 들어 http://127.0.0.1:8000/users/ 이렇게 해서 요청을 날리게 되면 class UserList에서 요청을 받게 될 것이다. 그러면 UserList에서는 
  User 모델 데이터를 자동으로 serializer, 즉 직렬화 해가지고 이걸 JSON으로 바꿔서 응답을 해주는 것이다. 그래서 이렇게 구현할 때는 클래스 내부에 queryset과 같이 모델 설정과 serializer 설정이
  필수라고 보면 된다.**
- djnago에 form이 있다면 DRF에는 Serializer가 있다고 보면 된다. 
- 위 클래스 내부에 permission_classes은 권한 설정이다. 그래서 permission_classes = [IsAdminUser] 해당 코드는 관리자만 가능하게 한다는 의미이다. 

```python
class UserList(generics.ListCreateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = [IsAdminUser]

    def list(self, request):
        # Note the use of `get_queryset()` instead of `self.queryset`
        queryset = self.get_queryset()
        serializer = UserSerializer(queryset, many=True)
        return Response(serializer.data)
 ```
 
 - **또한, 이렇게 ListCreateAPIView를 오버라이딩해서 커스텀할수도 있다.**

- **다시, API Guide - Generic Views를 보면 --> 사용할 수 있는 함수들이 있다. get_object(self)나 get_queryset(self), filter_queryset(self, queryset) 등 과 같은 함수들을 
  사용해볼 수 있다.** 
  - Mixin도 지원을 해준다. 그리고 django와 비슷하게 목록을 조회하는 ListAPIView, 상세 데이터 조회하는 RetrieveAPIView, 데이터 하나 삭제하는 DestroyAPIView, 데이터 하나 수정하는 UpdateAPIView 등도
    사용할 수 있다. 즉, RESTful하게 구현이 되어있는 View이다. 
    
    
- API Guide - Viewsets를 가보자.
  - Viewsets는 View가 모여있는 것이라고 보면 된다. 

```python
from django.contrib.auth.models import User
from django.shortcuts import get_object_or_404
from myapps.serializers import UserSerializer
from rest_framework import viewsets
from rest_framework.response import Response

class UserViewSet(viewsets.ViewSet):
    """
    A simple ViewSet for listing or retrieving users.
    """
    def list(self, request):
        queryset = User.objects.all()
        serializer = UserSerializer(queryset, many=True)
        return Response(serializer.data)

    def retrieve(self, request, pk=None):
        queryset = User.objects.all()
        user = get_object_or_404(queryset, pk=pk)
        serializer = UserSerializer(user)
        return Response(serializer.data)
```

- **이렇게 ViewSet를 상속받아 클래스를 정의하면, CRUD가 기본적으로 다 들어가게 된다.** 일반적으로 진짜 저수준의 CRUD는 UserViewSet 이렇게 모델 ViewSet를 사용하면 금방 개발이 가능하다.

- **ViewSet를 사용하면,**

```python
class UserViewSet(viewsets.ViewSet):
    """
    Example empty viewset demonstrating the standard
    actions that will be handled by a router class.

    If you're using format suffixes, make sure to also include
    the `format=None` keyword argument for each action.
    """

    def list(self, request):
        pass

    def create(self, request):
        pass

    def retrieve(self, request, pk=None):
        pass

    def update(self, request, pk=None):
        pass

    def partial_update(self, request, pk=None):
        pass

    def destroy(self, request, pk=None):
        pass
```

- **이렇게 기본적으로 list / create / retrieve / update / partial_update / destroy 와 같이 CRUD가 구현이 되어있는 것을 확인할 수 있다.**

- 그리고 get_permissions라는 함수를 사용해서, 요청에 따라 권한을 다르게 설정할 수 있다. 
  - 예를 들어, 우리 서비스의 회원 전체 데이터를 보는 것은 admin만 가능하게하고 그게 아니면 자기 자신만 볼 수 있게 할 수 있다. 

- **또한, 클래스 내부에  @action(detail=True, methods=['post']) / def set_password(self, request, pk=None): 이렇게 @action이라고 사용하는 경우가 있다.**
  - 이 경우는 별도로 우리가 RESTful하게 짜면서 뒤에 계층을 나눠가는 경우가 있다. 예를 들어서 특정 사용자가 작성한 포스트 게시글을 다 가져와라 라고 했을 때는, users에 id값 넣고 posts를 넣게 되면
    이 @action으로 정리를 해야된다. 
    
```python
class UserViewSet(viewsets.ModelViewSet):
    """
    A viewset that provides the standard actions
    """
    queryset = User.objects.all()
    serializer_class = UserSerializer

    @action(detail=True, methods=['post'])
    def set_password(self, request, pk=None):
        user = self.get_object()
        serializer = PasswordSerializer(data=request.data)
        if serializer.is_valid():
            user.set_password(serializer.validated_data['password'])
            user.save()
            return Response({'status': 'password set'})
        else:
            return Response(serializer.errors,
                            status=status.HTTP_400_BAD_REQUEST)
```

- **그래서 위의 경우에는, users에 set_password 라고 해서 --> url을 users/3/set_password 이렇게 만들지 않았을까 싶다.** 그래서 url를 그렇게 구성하면, set_password라는 함수를 호출하게 된다.



