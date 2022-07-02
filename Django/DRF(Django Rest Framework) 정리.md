## DRF란
- **Django REST Framework는 Django를 기반으로 REST API 서버를 만들기 위한 라이브러리이다.**
  - DRF는 어디까지나 Django를 기반으로 한 라이브러리이기 때문에 전반적으로 Django의 개발 흐름을 따라가게 된다.

- API는 웹뿐만 아니라 앱과 같은 다양한 플랫폼의 백엔드 서비스를 위해 JSON과 같은 규격화된 데이터를 제공한다. 따라서 DRF를 사용하면 기존에는 자체적인 웹 템플릿에게 바로 데이터를 전달해주는 Django 프로젝트에 국한되었던 것을 -> JSON과 같은 양식으로 다양한 플랫폼의 클라이언트에게 데이터를 제공해줄 수 있는 API 서버를 만들 수 있게 된다.


<br>

### 상태 코드
- 상태 코드는 요청에 대한 응답이 어떤 상태인지 나타내주는 규격화된 코드값이다. HTTP에서 정의하고 있는 값으로, HTTP를 사용하는 모든 웹 개발자들이 통용적으로 사용하고 있다.
- 따라서, DRF의 Response에는 이 상태 코드가 꼭 포함되어 있다. 몇 가지 대표적인 상태 코드들은 아래와 같다.

```python
HTTP_200_OK
# 데이터를 요청하는 GET 요청이 정상적으로 이뤄졌을 때 응답에 나타나는 상태값

HTTP_201_CREATED
# 데이터를 생성하는 POST 요청이 정상적으로 이뤄졌을 때 응답에 나타나는 상태값

HTTP_206_PARTIAL_CONTENT
# 데이터를 일부 수정하는 PATCH 요청이 정상적으로 이뤄졌을 때 응답에 나타나는 상태값

HTTP_400_BAD_REQUEST
# 잘못된 요청을 보냈을 때(클라이언트가) 응답에 나타나는 상태값

HTTP_401_UNAUTHORIZED
# 인증이 필요한데 인증 관련 내용이 요청에 없을 때 응답에 나타나는 상태값

HTTP_403_FORBIDDEN
# 클라이언트가 접근하지 못하도록 막아놓은 곳에 요청이 왔을 때 응답에 나타나는 상태값

HTTP_404_NOT_FOUND
# 클라이언트가 요청을 보낸 곳이 잘못된 URL일 때(즉, 리소스가 없을 때) 응답에 나타나는 상태값

HTTP_500_INTERNAL_SERVER_ERROR
# 서버 쪽에서 코드가 잘못되었을 때 응답에 나타나는 상태값
```

<br>

### HTTP의 헤더
<img width="189" alt="image" src="https://user-images.githubusercontent.com/95380638/174439996-b0df3f13-a391-4d62-be5c-303222034988.png">

- **위와 같이 Hello Api 화면에 보여지는 데이터들이 바로 HTTP의 헤더 데이터이다.** 
- HTTP는 웹의 프론트엔드와 백엔드 간 요청/응답을 위한 프로토콜이다. HTTP가 요청/응답을 할 때 누구나 같은 방식으로 통신을 하기 때문에 통일된 규격이 필요하고, 그 규격 내에 있는 주요 정보를 담아 놓은 것이 바로 HTTP의 헤더이다.
  - 요청/응답에 대한 결과 상태 및 데이터의 타입, 상태 코드 등이 전부 HTTP 헤더에 포함된다.

<br>

### Django VS DRF 차이점
- **Pure Django**
  - 개발 목적 : 웹 풀스택 개발
  - 개발 결과 : 웹 페이지를 포함한 웹 서비스
  - 응답 형태 : HTML
  - 다른 파일 : templates

- **Django REST Framework**
  - 개발 목적 : 백엔드 API 서버 개발
  - 개발 결과 : 여러 클라이언트에서 사용할 수 있는 API 서버
  - 응답 형태 : JSON
  - 다른 파일 : serializers.py

<br>

* * *

## Serializer 작성하기

<br>

### DRF Serializer
- **Serializer의 사전적 의미는 직렬화이다. 직렬화는 Django 프로젝트에서 내가 만든 모델로부터 뽑은 queryset, 즉 모델 인스턴스를 JSON 타입으로 바꾸는 것이다.**
  - **즉, serializer가 2개라면 View에서 2개의 객체를 보내줘야 한다.**
- DRF 내에서 데이터가 저장될 때는 Django의 모델을 통해 저장된다. 따라서 Django에서의 데이터는 JSON과 같은 포맷이 아닌, python 객체의 형태로 저장이 된다.
- 우리가 만들 API 서버는 이러한 데이터들을 클라이언트에게 보내주는 역할을 해야한다.
  - 이 때, 어떤 작업도 하지 않고 데이터를 그대로 보내준다면 클라이언트는 읽지도 못하는 python 데이터를 받게될 것이다.
  - **따라서 이를 위해 python 데이터를 읽을 수 있는 문자열(JSON 등)로 변환하여 보내주어야 하고, 이렇게 python 데이터 객체를 문자열로 변환하는 작업을 직렬화, Serializer라고 이해하면 된다.**

- **반대로 클라이언트가 데이터를 DRF 서버에 보내주는 경우도 있다.**
  - ex) POST 요청을 보내는 API를 클라이언트가 사용해 데이터를 생성하는 경우, 클라이언트는 API 요청에 데이터를 JSON 등 문자열 형태로 입력하여 보내줄 것이다.
- DRF API 서버 입장에서는 이 문자열을 그대로 모델을 통해 저장할 수 없다. 모델을 통해 저장하려면 데이터가 python 객체의 형태여야 하기 때문이다. **따라서 앞선 경우와 반대로 JSON 등 문자열을 python 데이터 객체로 변환하는 작업이 필요한데, 이것을 역직렬화, Deserializer라고 한다.**

- 우리가 앞으로 만들 Serializer는 직렬화와 역직렬화 기능을 동시에 갖고 있다. 즉, 클라이언트와 서버 API 간 데이터 양식을 맞춰주는 변환기라고 생각하면 된다.
  - [관련해서 좋은 글](https://velog.io/@jewon119/TIL136.-DRF-Serializer%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC)

- **모델을 구성하고 View를 작성하기 전에, 이 단계에서 Serializers.py를 만들고 Serializer를 구상하게 된다.**
  - 하나의 Serializer 클래스에는 모델과 같이 필드를 설정할 수 있고, 클래스 내부에 함수도 직접 정의할 수 있다.

```python
class BookSerializer(serializers.Serializer):
    bid = serializers.IntegerField()
    title = serializers.CharField(max_length=50)
    author = serializers.CharField(max_length=50)
    category = serializers.CharField(max_length=50)
    pages = serializers.IntegerField()
    price = serializers.IntegerField()
    published_date = serializers.DateField()
    description = serializers.TextField()

    def create(self, validated_data):
       return Book.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.bid = validated_data.get('bid', instance.bid)
        instance.title = validated_data.get('title', instance.title)
        instance.author = validated_data.get('author', instance.author)
        instance.category = validated_data.get('category', instance.category)
        instance.pages = validated_data.get('pages', instance.pages)
        instance.price = validated_data.get('price', instance.price)
        instance.published_date = validated_data.get('published_date', instance.published_date)
        instance.description = validated_data.get('description', instance.description)
        instance.save()
        
        return instance
```

- **시리얼라이저는 python 모델 데이터를 JSON으로 바꿔주는 변환기이기 때문에 모델 데이터의 어떤 속성을 JSON에 넣어줄지 선언을 해줘야 한다. 그래서 시리얼라이저에서 필드를 선언해주는 것이다.**
- **그리고 create나 update와 같은 함수는 나중에 POST 요청으로 들어온 데이터를 다시 python 모델 형태로 역직렬화하여 데이터베이스에 접어넣을 때 사용되는 함수이다. 이렇게 선언하고 나중에 serializer.save() 이렇게 데이터를 저장할 수 있다.**

<br>

### serializers.ModelSerializer
```python
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['bid', 'title', 'author', 'category', 'pages', 'price', 'published_date', 'description',]
```

- 위에서 보듯이 serializers.Serializer를 사용하게 되면 일일이 필드들을 정해주고 작성해야할 코드들이 많아진다. 그래서 개발자들은 더 나은 대안인 serializers.ModelSerializer를 사용한다.
- **ModelSerializer는 모델의 내용을 기반으로 동작하는 시리얼라이저이다. 이를 통해 코드의 중복을 줄일 수 있고 필드 선언을 모델에서 이미 했기 때문에 ModelSerializer에서는 간단하게 작업할 수 있다.**

```python
class TodoSimpleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = ('id', 'title', 'complete', 'important')


class TodoDetailSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = ('id', 'title', 'description', 'created', 'complete',
                  'important')


class TodoCreateSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = ('title', 'description', 'important')
```
- **이런식으로 시리얼라이저마다 다르게 필드를 설정할 수 있다.**
  - ex) TodoCreateSerializer는 Todo를 생성할 때 필요한 입력 값이 title과 description, important라서 3개의 필드만 정의한 것이다.


<br>

* * *

## View 작성하기
- **기본적으로 View 클래스를 기준으로 URL을 분리한다고 생각하면 편하다.**

<br>

### @api_view()
```python
@api_view(['GET'])
def HelloAPI(request):
    return Response("hello world!")
```

- **위와 같이 @api_view(['GET'])라는 데코레이터는, helloAPI라는 함수가 API로 동작하기 위해 앞뒤로 함수를 감싸 실제 API로 동작할 수 있도록 해준다.** 따라서 HelloAPI라는 함수가 GET 요청을 받을 수 있는 API라는 것을 표시해준다고 보면 된다.

- **여기서 request 객체는 사용자가 우리 DRF API 서버로 요청을 보낼 때, 요청이 GET, POST 등 어떤 타입인지, 사용자가 요청을 보낼 때 무슨 데이터를 함께 보내주었는지 등 정보를 담고 있다.**
  - ex) request.method / request.data로 접근 가능

- **그리고 결과를 반환할 때는 Response라는 클래스를 사용한다.**
  - request와 마찬가지로 Response에는 응답에 대한 정보가 담겨져 있다. response.data에는 응답에 포함되는 데이터 / response.status에는 응답에 대한 상태가 나타난다.

<br>

### APIView
- django에서든 DRF에서든 View를 개발하는 것은 크게 함수 기반 View인 FBV와 / 클래스 기반 View인 CBV로 나눌 수 있다.
- **DRF에서는 2가지를 모두 도와주는 APIView라는 것이 있다.**

- **함수 기반 View에서는 @api_view와 같이 데코레이터 형태로 APIView를 사용할 수 있다.**
```python
@api_view(['GET', 'POST'])  # GET/POST 요청을 처리하게 만들어주는 데코레이터
def booksAPI(request):
    if request.method == 'GET':      # GET 요청일 때
        books = Book.objects.all()   # Book 모델로부터 전체 데이터 가져오기
        serializer = BookSerializer(books, many=True)                 # 시리얼라이저에 전체 객체들을 한 번에 집어넣기(직렬화, many=True) -> 이 때, python 객체를 JSON 형태로 직렬화 한다는 것
        return Response(serializer.data, status=status.HTTP_200_OK)
    elif request.method == 'POST':   # POST 요청일 때
        serializer = BookSerializer(data=request.data)                # POST 요청으로 들어온 데이터를 시리얼라이저에 집어넣기
        if serializer.is_valid():                                     # 유효한 데이터라면
            serializer.save()                                         # 시리얼라이저의 역직렬화를 통해 save(), 이 때 ModelSerializer의 기본 create() 함수가 동작
            return Response(serializer.data, status=status.HTTP_201_CREATED)   # 201 메시지를 보내며 성공
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST) # 400은 잘못된 요청일 경우 응답

@api_view(['GET'])
def bookAPI(request, bid):
    book = get_object_or_404(Book, bid=bid)                           # bid = id인 데이터를 Book에서 가져오고 없으면 404 에러
    serializer = BookSerializer(book)                                 # 시리얼라이저에 객체를 집어넣기(직렬화)
    return Response(serializer.data, status=status.HTTP_200_OK) 
```
- **/book/ 주소를 사용할 두 API에 대한 처리는 booksAPI()라는 함수에서 한 번에 처리한다.**
  - 데코레이터로 GET, POST를 함께 처리할 수 있도록 설정했고 조건문을 통해 해당 요청이 GET인지 POST인지에 따라 다르게 처리한다.
  - **GET 요청은 도서 전체 정보를 가져오니까 모델로부터 객체들을 가져와 시리얼라이저에 집어넣어서 -> 직렬화를 하고 그렇게 가공된 데이터를 결과로 응답한다. ex) serializer = BookSerializer(books, many=True)**
  - **시리얼라이저에 넣을 때, many=True 옵션을 넣으면, 여러 객체들에 대한 처리를 할 수 있도록 해준다.**
  - **POST 요청은 오히려 요청으로 들어온 데이터를 역직렬화하여 모델에 집어넣어야 하므로 -> 먼저 시리얼라이저에 serializer = BookSerializer(data=request.data) 이렇게 request.data를 넣어준다.**
  - 그리고 시리얼라이저의 is_valid() 기능을 통해 들어온 데이터가 모델에 맞는 유효한 데이터라면 이를 저장한다.
  - 마지막으로 serializer.save()는 기본적인 create() 함수를 실행시키는 ModelSerializer의 기능이다. 데이터가 잘 저장되었다면 201 메시지를 보내고 아니면 400 메시지를 보내서 마무리한다.

- **bookAPI 함수도 마찬가지이다.**
  - 특정 bid의 책 데이터를 가져와서 함수의 인자로 bid를 넘겨받아 모델에서 찾는다. 그리고 찾은 객체를 serializer = BookSerializer(book) 이렇게 직렬화해준다.

<br>

- **그리고 클래스 기반 View에서는 APIView라는 클래스를 상속받는 클래스의 형태로 생성할 수 있다.**
```python
class BooksAPI(APIView):
    def get(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK) 
    def post(self, request):
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED) 
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class BookAPI(APIView):
    def get(self, request, bid):
        book = get_object_or_404(Book, bid=bid)
        serializer = BookSerializer(book)
        return Response(serializer.data, status=status.HTTP_200_OK)
```

- 함수형 View와 기능적으로는 큰 차이가 없다. 
- **차이점은 클래스 내에 get과 post를 따로 정의해주기 때문에 데코레이터가 필요없고 해당 요청이 GET인지 POST인지 조건문으로 따져볼 필요가 없다.**

- get 메소드 내부에 있는 return Response(serializer.data, status=status.HTTP_200_OK) 해당 코드에서 **serializer.data를 변수에 담고 출력해보면, [해당 블로그](https://velog.io/@jewon119/TIL136.-DRF-Serializer%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC)에 나와있듯이 딕셔너리 형태의 객체로 받을 수 있게 된다. ReturnDict이라는 타입으로 DRF에서 지원하는 딕셔너리 형태이다.**
  - 그 다음 이제 JSON 포맷의 Byte String 타입으로 클라이언트에 전달하기 위해 JSONRenderer를 사용할 수 있다.

<br>

- **Serializing과 Deserializing 비교**
  - **Serializing**은 Database로부터 Object를 가져와 Serializer의 instance 인자에 전달하여 dict 데이터로 변환한다. 이 dict 데이터를 JSON 포맷의 Byte로 변환하여 클라이언트에 전달하는 흐름이며, 이 과정에서 JSONRenderer가 사용된다.
  - 반대로, **Deserializing**는 클라이언트로 전달받은 JSON 포맷의 Byte 데이터를 JSONParser의 parse 매서드를 통해 우선 dict 데이터로 변환한다. 이 dict 데이터를 바로 사용하는 것이 아닌, Serializer의 data 인자에 전달하여 is_valid 매서드로 유효성 검사를 진행한다. 유효성 검사에 문제가 있다면 errors의 에러 정보가 담기지만, 유효성 검사를 통과했다면 validated_data 속성에 데이터가 존재한다. validated_data를 만들고 나서야 이 데이터를 사용해서 모델에 전달해 인스턴스를 만들고, Database에 저장한다.

```python
class TodosAPIView(APIView):
    def get(self, request):
        todos = Todo.objects.filter(complete=False)
        serializer = TodoSimpleSerializer(todos, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        serializer = TodoCreateSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class TodoAPIView(APIView):
    def get(self, request, pk):
        todo = get_object_or_404(Todo, id=pk)
        serializer = TodoDetailSerializer(todo)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def put(self, request, pk):
        todo = get_object_or_404(Todo, id=pk)
        serializer = TodoCreateSerializer(todo, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class DoneTodosAPIView(APIView):
    def get(self, request):
        dones = Todo.objects.filter(complete=True)
        serializer = TodoSimpleSerializer(dones, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)


class DoneTodoAPIView(APIView):
    def get(self, request, pk):
        done = get_object_or_404(Todo, id=pk)
        done.complete = True
        done.save()
        serializer = TodoDetailSerializer(done)
        return Response(status=status.HTTP_200_OK)
```

- **위에는 Todo라는 모델 CRUD API를 APIView로 작성한 예시이다.**


<br>

### URL 연결하기
- **함수형 View를 연결할 때는 다음과 같다.**

```python
urlpatterns = [
    path("hello/", helloAPI),
    path("fbv/books/", booksAPI),
    path("fbv/book/<int:bid>/", bookAPI),
]
```

- **그리고 클래스형 View를 연결할 때는 다음과 같다. 함수형 View와 달리 path에 등록할 때 .as_view()를 사용한다.**

```python
urlpatterns = [
    path("hello/", helloAPI),
    path("fbv/books/", booksAPI),
    path("fbv/book/<int:bid>/", bookAPI),
    path("cbv/books/", BooksAPI.as_view()),
    path("cbv/book/<int:bid>/", BookAPI.as_view()),
]
```

- **함수형 View이든, 클래스형 View이든 결국엔 각 request의 method(GET, POST 등)에 따라 처리할 수 있도록 작성된다.**
  - ex) 127.0.0.1/books/ => GET(전체 목록) / POST(생성)
  - ex) 127.0.0.1/books/1 => GET(가져오기) / PUT(수정) / DELETE(삭제)

- **일반적인 DRF 개발 절차는 Model -> Serializers -> Views -> Urls 라고 볼 수 있다.**

- **지금까지는 DRF의 기본 페이지로 API 테스트를 진행했지만, Insomnia라는 API 테스트 무료 툴도 존재한다.**

<br>

### DRF의 다양한 뷰
- 일반적으로 URL과 Method의 조합은 크게 총 5개의 조합이 나온다.

```python
# 도서 전체 목록 가져오기 (GET /books/), (list)
# 도서 1권 정보 등록하기 (POST /books/), (create)
# 도서 1권 정보 가져오기 (GET /book/1/), (retrieve)
# 도서 1권 정보 수정하기 (PUT /book/1/), (update)
# 도서 1권 정보 삭제하기 (DELETE /book/1/), (destroy)
```

- **여기서 어떤 모델에 대해 retrieve를 한다고 하면 우리는 그 모델의 1개 객체 정보를 가져온다고 생각하면 된다.**
- **일반적으로 DRF로 API를 개발할 때 만들어야 하는 기능은 위의 5가지이다. 그래서 개발자들은 이를 최대한 편하고 쉽게 만들고자 mixins, generics, Viewset까지 발전해왔다.**


<br>

### DRF mixins
- **우리가 위에서 작성한 클래스 기반 View를 다시보면 중복되는 코드들이 있다는 것을 알 수 있다. 그래서 이러한 중복을 제거하기 위해 DRF에서는 mixins라는 것을 미리 정의하고 있다.**
  - **mixins는 APIView에서 request의 method마다 시리얼라이저 코드를 작성하는 것을 줄이기 위해 클래스 레벨에서 시리얼라이저를 등록하고 있다.**
  - 그래서 각 method에는 시리얼라이저 코드를 작성하는 대신 각 method별 mixin에 연결하여 사용하기만 하면 된다.

```python
from rest_framework import generics
from rest_framework import mixins

class BooksAPIMixins(mixins.ListModelMixin, mixins.CreateModelMixin,generics.GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):          # GET 메소드 처리 함수(전체 목록)
        return self.list(request, *args, **kwargs)    # mixins.ListModelMixin과 연결
    def post(self, request, *args, **kwargs):         # POST 메소드 처리 함수 (1권 등록)
        return self.create(request, *args, **kwargs)  # mixins.CreateModelMixin과 연결

class BookAPIMixins(mixins.RetrieveModelMixin, mixins.UpdateModelMixin, mixins.DestroyModelMixin, generics.GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    lookup_field = 'bid'                                 # django의 기본 모델 pk가 아닌 여기서는 bid를 pk로 사용하고 있으니 lookup_field로 설정

    def get(self, request, *args, **kwargs):             # GET 메소드 처리 함수(1권)
        return self.retrieve(request, *args, **kwargs)   # mixins.RetrieveModelMixin과 연결
    def put(self, request, *args, **kwargs):             # PUT 메소드 처리 함수(1권 수정)
        return self.update(request, *args, **kwargs)     # mixins.UpdateModelMixin과 연결
    def delete(self, request, *args, **kwargs):          # DELETE 메소드 처리 함수(1권 삭제)
        return self.destroy(request, *args, **kwargs)    # mixins.DestroyModelMixin과 연결
```

- **이렇게 동일한 도서 정보 API를 mixins로 다시 만들어봤다.**
- 우선 generics와 mixins를 import 해서 클래스를 만들 때 상속해준다.
  - BooksAPIMixins 클래스의 경우, ListModelMixin과 CreateModelMixin을 상속하고 / BookAPIMixins 클래스의 경우, RetrieveModelMixin과 UpdateModelMixin 그리고 DestroyModelMixin를 상속하고 있다.
  - 공통적으로는 generics.GenericAPIView를 상속하고 있다.

- **그리고 공통적으로 Mixins 클래스 내부 변수에는 queryset과 serializer_class라는 변수가 있다.**
  - queryset에는 모델에 질문을 보내 받아온 데이터가 들어가는데 일반적으로 그냥 모든 데이터를 불러온다. ex) queryset = Book.objects.all()
  - 그리고 serializer_class에는 해당 API에서 사용할 시리얼라이저를 설정한다. ex) serializer_class = BookSerializer 이렇게 설정해준다.

- **또한, 각 클래스 내부의 함수명은 메소드명과 동일하다. ex) def get**
  - 이 부분은 클래스형 View와 동일하지만 함수의 내용이 없어지고 바로 return을 한다. 이 때, return 하는 부분을 보면 각각 다른 것을 반환하고 있는데, 이는 각 메소드 별 처리하는 기능에 따라, 상속받아 온 mixin 중 어떤 것에 연결할 것이냐에 따라 달라지게 된다.

- **이렇게 Mixin를 사용하면, API 테스트 화면에서 DRF가 함께 제공해주는 템플릿으로 일일이 JSON을 입력하지 않고 필드마다 입력할 수 있는 Form이 생성된다.**

<br>

### DRF generics
- mixins 방법은 REST API의 메인 기능을 코드 10여줄로 끝낸다는 점에서 획기적이고 간단했다. 그러나 이것마저도 귀찮다는 생각을 한 개발자들이 있었다. mixins 방법에서 사용할 mixins를 상속받는데 한 번에 2~3개씩 상속을 받아야 하기 때문이다.
- **그래서 DRF에는 mixins를 조합해서 미리 만들어둔 일종의 mixins 세트가 있다. 총 9개의 조합 종류가 있다. 이 종류가 generics 하위에 존재한다.**

```python
# (1) generics.ListAPIView (전체 목록)
# (2) generics.CreateAPIView (생성)
# (3) generics.RetrieveAPIView (1개)
# (4) generics.UpdateAPIView (1개 수정)
# (5) generics.DestroyAPIView (1개 삭제)
# (6) generics.ListCreateAPIView (전체 목록 + 생성)
# (7) generics.RetrieveUpdateAPIView (1개 + 1개 수정)
# (8) generics.RetrieveDestroyAPIView (1개 + 1개 삭제)
# (9) generics.RetrieveUpdateDestroyAPIView (1개 + 1개 수정 + 1개 삭제)
```

- 그래서 우리가 앞서 만든 5가지의 기능을 generics로 만든다면, 6번과 9번을 사용하면 된다.

```python
from rest_framework import generics

class BooksAPIGenerics(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

class BookAPIGenerics(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    lookup_field = 'bid'
```

- **위와 같이 여러 개의 mixins를 각각 상속하던 방식에서 이제는 mixins가 조합된 generics를 한 번만 상속받는 것으로 끝냈다.** 
  - ex) class BookAPIGenerics(generics.RetrieveUpdateDestroyAPIView):
- 그런데 return 구문도 아예 사라졌다. 앞서 여러 개의 mixins를 사용하던 방식에서는 각 mixins를 메소드에 매칭시키는 과정이 필요했고 그게 return 구문에 있었다. 
- **이제는 mixins가 조합된 generics를 사용하다 보니 -> 어차피 List + Create 이면 GET과 POST 메소드가 사용된다는 것을 이미 알고 있는 것이다.**
  - 실제로 DRF 내부의 generics.ListCreateAPIView의 코드를 보면, 내부에 def get / def post가 정의되어있다.

- **결국, generics는 mixins를 조합하기만 해놓은 코드라고 할 수 있다.**


<br>

### DRF Viewset & Router
- generics만으로 충분히 코드를 간소화했지만, 더 줄일 수 있는 부분이 있다. 
- 지금까지는 하나의 클래스가 하나의 URL를 담당하는 방식이었다. URL마다 클래스를 만들고 처리할 수 있게 했다.
- **그러다보니 queryset과 serializer_class 부분이 겹치게 되었다. 그래서 하나의 클래스로 하나의 모델을 전부 처리해줄 수 있다면 겹치는 부분이 사라질텐데 이게 바로 Viewset이다.**
  - Viewset은 말그대로 View의 집합이다. 기본적인 모습은 클래스형 View의 기본형과 같다. 

```python
from rest_framework import viewsets

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

- 이렇게 Book 모델 REST API가 4줄로 줄어들었다.
- **ModelViewSet를 상속받아 클래스를 만들고 queryset과 serializer_class를 설정해주면 모델에 대한 기본적인 REST API가 완성된다.**
  - 이러한 ModelViewSet의 내부 구조를 보자.

```python
class ModelViewSet(mixins.CreateModelMixin, mixins.RetrieveModelMixin, mixins.UpdateModelMixin, mixins.DestroyModelMixin, mixins.ListModelMixin, GenericViewSet):
...
```

- **이렇게 DRF 내부에서 ModelViewSet를 찾아보면, ViewSet도 결국엔 Mixin을 사용하고 있다는 것을 알 수 있다. 결국에는 앞서 배운 mixin을 기반으로 작성되었다.**

- **이러한 ViewSet을 URL로 연결할 때는 조금 다르다.**

```python
from rest_framework import routers
from .views import BookViewSet

router = routers.SimpleRouter()
router.register('books', BookViewSet)

urlpatterns = router.urls
```

- **라우터 객체를 만들고 라우터에 우리의 ViewSet를 등록한다. 그리고 urlpatterns에는 router.urls를 include해서 작성하면 된다.**

- **이렇게 ViewSet과 Router를 쓰는 것으로 얻을 수 있는 장점들이 있다.**
  - 하나의 클래스로 하나의 모델에 대한 내용을 전부 작성할 수 있고 queryset이나 serializer_class 등 겹치는 부분을 최소화할 수 있다.
  - 라우터를 통해 URL를 일일이 지정하지 않아도 일정한 규칙의 URL을 만들 수 있다.

<br>

### SerializerMethodField
- **Serializer 클래스 내부에 변수를 SerializerMethodField로 추가로 정의하면, 클래스 내부에 있는 메소드를 호출하게 된다.**
- 만약 SerializerMethodField 인자에 아무것도 넣지 않는다면 “def get_필드이름” 이렇게 get_필드이름의 메소드를 호출하게 된다. 그래서 그 호출한 메소드의 값을 가져올 수 있게 된다.
  - 해당 메소드는 self외에도 obj라는 argument를 받는다. obj는 직렬화되는 객체를 의미한다.

- [참고 블로그](https://velog.io/@oen/SerializerMethodField-%EA%B3%B5%EC%8B%9D%EB%AC%B8%EC%84%9C-%EB%B2%88%EC%97%AD)





