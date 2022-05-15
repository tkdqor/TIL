## HTTP Method와 ViewSets (1)
- 이제는 ViewSets에 대해서 알아보기. https://www.django-rest-framework.org/api-guide/viewsets/ 공식문서 참고.

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

- ViewSets은 이렇게 기본적인 메소드들이 다 담겨있다고 보면 된다.
- "라우팅이 요청에 사용할 컨트롤러를 결정한 후, 컨트롤러 요청을 이해하고 적절한 출력을 행사할 책임이 있다." 는 말이 공식 문서에 있는데, 라우터가 요청을 하면 ViewSets이 알아서 처리하라는 뜻이다.
- retrieve부터는 pk값을 받아야 한다. 특정 하나의 데이터가 필요하기 때문이다.


```python
def get_permissions(self):
    """
    Instantiates and returns the list of permissions that this view requires.
    """
    if self.action == 'list':
        permission_classes = [IsAuthenticated]
    else:
        permission_classes = [IsAdminUser]
    return [permission() for permission in permission_classes]
```

- 또한 이렇게 액션이 list일 경우에는 인증된 사용자만 할 수 있고 그게 아니면 어드민만 해라라고 설정가능. 사용자 조회 시 사용하면 될듯하다.


```python
from django.contrib.auth.models import User
from rest_framework import status, viewsets
from rest_framework.decorators import action
from rest_framework.response import Response
from myapp.serializers import UserSerializer, PasswordSerializer

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

    @action(detail=False)
    def recent_users(self, request):
        recent_users = User.objects.all().order_by('-last_login')

        page = self.paginate_queryset(recent_users)
        if page is not None:
            serializer = self.get_serializer(page, many=True)
            return self.get_paginated_response(serializer.data)

        serializer = self.get_serializer(recent_users, many=True)
        return Response(serializer.data)
```

- 또한 이렇게 action이 True이면 set_password 메소드가 실행된다. 그래서 user 객체를 가져온다. 그리고 비밀번호를 수정해줄 수 있다. 
- 그리고 action의 detail이 False이면 -> 즉, 1개의 디테일이 아닌 여러 유저들의 목록을 가져온다. 그래서 최근 사용자 순서대로 불러오는 메소드이다. 


```python
@action(detail=True, methods=['post'], permission_classes=[IsAdminOrIsSelf])
    def set_password(self, request, pk=None):
       ...
```

- **위의 코드는 API 권한과 관련된 코드이다. 우리가 만드는 API들은 권한을 체크해주는 것이 중요하기 때문이다.**
  - **인증은 우리 시스템에 접근할 수 있느냐이고 / 권한은 해당 기능에 대한 권리가 있느냐이다.**


### ModelViewSet
```python
class AccountViewSet(viewsets.ModelViewSet):
    """
    A simple ViewSet for viewing and editing accounts.
    """
    queryset = Account.objects.all()
    serializer_class = AccountSerializer
    permission_classes = [IsAccountAdminOrReadOnly]
```

- 우리가 가장 많이 사용하게 될 ModelViewSet.
