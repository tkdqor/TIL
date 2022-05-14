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
