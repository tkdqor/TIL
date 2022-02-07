## MVT pattern
- 레이아웃을 통해 우리가 가진 정보를 사용자에게 어떠한 모습으로 전달할 것인가가 결정된다.
- 또한, 그 레이아웃을 채워줄 데이터가 필요하게 된다.
- 마지막으로 웹 서비스를 만들기 위해서는 레이아웃과 데이터 이외에도 다양한 기능적인 요소들이 필요하다. 버튼을 누르면 로그인 처리를 한다던가, 좋아요 처리를 해주고 팔로우 / 언팔로우 기능을 제공, 장바구니에 상품 담기 등 
  웹 서비스가 돌아가기 위해 반드시 수행되어야 할 절차와 로직이 필요하다.
  - 그래서 일반적으로 웹 서비스는 레이아웃 / 데이터 / 로직 이렇게 3가지의 구성요소로 이루어져있다.

- 여러 웹 프레임워크는 이 3가지를 엮어서 사용하게 하는데 django는 어떻게 구성되는지 살펴보자. Model / View / Template의 앞글자를 따서 MVT pattern이라고 한다.


### Model
- 웹 페이지에서 보여줄 데이터와 관련된 것이 django의 model이다. 데이터베이스에 저장되어있는 데이터를 python 코드로 접근하고 python 코드로 조작할 수 있게끔 해준다.
  - 우리가 웹 서비스를 이용하면서 봤던 다양한 데이터들은 모두 다 어딘가에 저장이 되어있다. 가장 대표적인 저장 방식이 바로 테이블의 형태로 데이터를 저장하는 방식이다. 그래서 데이터베이스에 이러한 테이블의 형태로 데이터를 저장하려면
    원래는 SQL이라고 부르는 데이터베이스 전용 언어를 별도로 사용해야 한다.
  - 하지만 django 모델을 사용하게 되면, models.py와 같이 python클래스로 만들고자 하는 테이블의 형태를 관리할 수 있고 python 코드를 통해 새로운 데이터를 추가하거나 기존의 데이터를 조회할 수 있다.
  

### Template
- django에서 레이아웃의 개념으로 사용되는 것이 바로 Template이라는 개념이다. 
- 웹 페이지 화면에 보여지는 작성자의 아이디나 글, 작성일자와 같은 것들은 어떤 데이터가 채워지느냐에 따라서 값이 매번 바뀐다. django template에서는 HTML 코드를 기반으로 전체적인 레이아웃을 잡고, 매번 바뀌는 
  부분에 데이터가 들어갈 공간을 마련해둘 수 있다. 
- django template language를 사용하게 되면, if문을 통해 데이터 표시 여부를 설정할 수 있고, for문으로 반복되게끔 할 수도 있다.


### View
- 웹 서비스가 동작하기 위해서는 다양한 비즈니스 로직이 필요한데, django에서는 이러한 로직 구성을 View라고 부른다.
- 우리가 원하는 어떤 로직이든지 간에 view에서 작성가능하다.
  - ex) 사용자가 최근에 작성한 게시물 TOP 3는 -> posts = Post.objects.all().order_by('-created_at')[:3] 이렇게 표현가능하다.
- django view는 HTTP Request에 대한 최종 HTTP Response를 결정하는 부분이기도 하다. 단순히 텍스트를 return 할 수도 있고, django의 template인 HTML파일을 return 할 수도 있다.


### Django framework Flow
- Client의 모든 HTTP Request를 django Framework가 받게 된다. 그리고 해당 HTTP Request를 url을 기반으로 어떤 view가 처리해야 할지를 매핑한다.
  - server가 모든 HTTP Request를 처리할 필요는 없다. 해당 Request가 server가 처리할 수 있는 Request인지 아닌지를 판단하고, 처리할 수 있다면 그 Request를 어떤 view가 처리할지 결정해준다. 매핑되어 있지 않은 url의 경우에는 해당 정보가 없다는 페이지를 띄우게 된다.
- 그렇게 결정된 view에서 template이나 model를 사용하지 않는 경우에는, 해당 view에서 모든 처리를 마무리하고 client에게 HTTP Response를 전달하게 된다.

- urls.py의 urlpatterns 라는 변수가 있는데, django server에서 처리할 수 있는 url pattern의 목록을 관리하는 변수라고 할 수 있다. 
```python
urlpatterns = [
    path = ('posts/', views.index),
]
```

- 위와 같이 작성된 경우, client가 이 posts/라는 url로 HTTP Request를 보냈을 경우에는 -> views 모듈안에 있는 index 함수가 이 HTTP Request를 처리하라고 설정해준 코드이다.
  - **주의할 점은, 일반적으로 함수를 호출해서 사용하게 되지만 urls.py에서는 괄호를 사용하지 않고 함수 자체를 입력해서 지정만 해주게 된다. ex) views.index() -> views.index**
  - **그 이유는, 함수를 호출하는 주체가 우리가 아닌 django server이기 때문이다. 또한, 함수를 지금 당장 호출하겠다는 것이 아니라 client가 posts/라는 url pattern를 가진 HTTP Request를 전달했을 때 그 때 비로소 이 views.index 함수를 호출하겠다 하고 지정하는 코드이기 때문이다.** 

- 그래서 우리가 localhost:8000/posts/ 라는 주소로 브라우저에 입력하게 되면, posts/ 라는 Request URL로 HTTP Request가 들어오게 되는 것이다.
  - 그렇게 되면 urls.py에 설정한대로 views 모듈의 index 함수가 호출되고 실행되는 것이다.
  - 그래서 우리가 새로고침 할 때 마다, Request가 매번 발생하고 index 함수가 매번 다시 실행된다. 

- 그리고 django가 index 함수를 포함해서 모든 view 함수를 실행할 때 첫번째 인자로 client가 전송한 HTTP Request에 대한 정보를 첫번째 인자로 전달해준다.
```python
def index(request):
    return HttpResponse('Hello!')
```

- 이 request에 client가 전송한 HTTP Request에 대한 정보가 모두 담겨있다고 보면 된다. 그래서 해당 요청이 GET 또는 POST 방식인지 등 HTTP Request에 대한 정보가 필요하다면 함수 내부에 이 request를 참조하면 된다.
- 위에서는 HttpResponse로 단순한 텍스트를 client에게 전달해주고 있다.


### View 구성
- HTTP Response를 만드는 과정에서 데이터가 필요하다면, view에서 model를 사용해서 데이터를 CRUD 진행할 수 있다.
- 필요하다면 template인 HTML에 대입해서 완전한 웹 페이지를 만든 다음, HTTP Response로 줄 수 있다.

- 이러한 django의 MVT pattern은 -> 다른 웹 프레임워크에서도 MVC pattern이라는 이름으로 웹 서비스를 구성하는 방식으로 사용된다.
  - django의 Model / View / Template -> Model / Controller / View에 해당



