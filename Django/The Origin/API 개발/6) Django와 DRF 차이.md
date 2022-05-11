## Django와 DRF 차이
- 우리가 예전에 만들었던 계산기를 DRF로 만들어보기
- **우선 API를 테스트 할 때는 임의의 클라이언트를 대체해줄 수 있는 프로그램을 사용하기도 한다. 그래서 이번에는 "postman"이라는 프로그램을 사용해보자. 우리가 백엔드 개발을 하기 때문에 우리가 클라이언트를
  만들 수 없다. 그래서 클라이언트가 요청을 날리는 것 처럼 해주는 것이다.** 우리가 API 서버를 만들게 되면 클라이언트 플랫폼 의존성이 낮다. 웹앱이나 안드로이드, iOS 기타 디바이스가 요청을 날려도 결국
  서버에서는 특정한 경우를 제외하고 그냥 똑같은 요청으로 받아드리기 때문에 이 postman으로 날려도 동일한 환경으로 우리가 확인을 할 수 있다. 
- https://www.postman.com/ 해당 사이트에 들어가서 OS에 맞게 설치해주기. 


### Postman
- 상단의 Workspaces를 클릭하고 Create workspace를 클릭한다. 그리고 ProjectLion이라는 이름으로 새롭게 생성해준다. 
- 그리고 상단 + 버튼을 클릭하고 url를 새롭게 생성해보자.
  - 우리가 이전에는 http://localhost:8000/ 이렇게 입력하면 브라우저로 DRF 화면을 확인할 수 있었다. 그래서 postman에서도 http://localhost:8000/ 이 URL를 그대로 입력하고 GET 요청으로 
    Send를 클릭하면

<img width="915" alt="image" src="https://user-images.githubusercontent.com/95380638/167800264-54730b33-a7a8-4853-bf26-a6ddeb4dbe2a.png">

<img width="1054" alt="image" src="https://user-images.githubusercontent.com/95380638/167800471-50282056-322d-4d9c-ac3e-95a6bc7fe89a.png">


- 이렇게 DRF 내용이 뜨게 된다. http://localhost:8000/posts/ 라고 요청하면 POST모델 데이터도 뜨게 된다. 사실 우리가 지금까지 확인했던 브라우저 화면은, DRF에서 GUI적으로 보기 편하게 제공을
  해주는 것이다.
- 그리고 postman에서 위의 url 그대로 POST 방식으로 보낼 수도 있다. 근데 추가로 데이터를 입력해야 한다. Body라는 부분을 클릭해서 x-www-form-urlencoded를 클릭하고 넣어주면 된다. 여기에 우리가
  넣어야 할 key를 추가하고 값들을 넣어주면 된다.

<img width="866" alt="image" src="https://user-images.githubusercontent.com/95380638/167801624-5febe2c9-9c6e-476b-a2f0-7efade0cde4c.png">

- **그러면 새롭게 데이터가 생성된다. 응답값, status code는 201번으로 나왔다. 그리고 속도도 표시해준다.**
- 이렇게 postman이 클라이언트 역할을 대신 해줄 수 있다. 

- **우리가 나중에 백엔드 개발을 하게 되었을 때, 클라이언트 또는 백엔드 둘 중 한군데에서 문제가 되어 기능이 정상적으로 수행되지 않는 경우가 있다. 그래서 우리가 API 서버를 만지다가 오류가 났을 때는 ->
  postman으로 직접 클라이언트 역할을 해서 API 요청을 해보고 제대로 된건지 확인을 하고 제대로 되었다고 하면, 클라이언트 쪽에서 문제가 있다고 판단할 수 있다. 근데 만약 postman으로 요청을 했을 때 
  서버쪽 응답이 이상하다면 서버쪽에 문제가 있는 것이기 때문에 내가 직접적으로 대응해줘야 한다.** 그래서 postman을 이슈 발생 시 판단용으로 사용할 수 있다. 

- 그리고 postman의 경우에는 Collections를 만들어 놓고 서로 관련있는 url별로 구분지어서 확인할 수 있다. 

<img width="761" alt="image" src="https://user-images.githubusercontent.com/95380638/167804675-9d06f3e1-39c8-476e-8832-08c21183ed57.png">

<img width="724" alt="image" src="https://user-images.githubusercontent.com/95380638/167805181-51fe7b76-6b27-4768-8eb8-de85cf186f1e.png">


- **이런식으로 만들어놓고 나랑 협업하는 개발자간의 소통을 할 수도 있다. 내가 프론트엔드 개발자한테 "제가 Post - List API 만들었습니다. 한 번 사용해주세요!" 이렇게 얘기해주면 된다.**
  - **그러면 postman API 화면에 method 나오고 URL 나오고 인증이나 Headers, 파라미터 등 내가 정해놓은 것들을 나혼자 쓰는 게 아니고 나랑 같이 하는 동료들과 소통할 수 있고 API 명세서라고 해서 API 문서 대체용으로 이 postman을 남겨놓고 나중에 오는 사람들이 사용할 수 있도록 문서화를 시킬 수가 있다.**


### API 문서화
- 요즘에는 postman를 쓰기도 하지만 그러지 않고 일반 문서로 만드는 경우도 굉장히 많다. 
- 또한, 최근에 굉장히 인기있는 swagger라는 코드베이스로 해서 API 명세서를 만들어주는 것도 있다. 



### liongram-api에 계산기 만들기 - 함수 기반 API View
- liongram-api 내부 posts 앱에 있는 views.py에 코드를 추가해보자.

```python
from rest_framework.response import Response
from rest_framework.decorators import api_view
...

@api_view()
# 계산기 기능 / DRF로 구성
def calculator(request):
    # 1. 데이터 확인
    num1 = request.GET.get('num1', 0)
    num2 = request.GET.get('num2', 0)
    operators = request.GET.get('operators')

    # 2. 계산
    if operators == '+':
        result = int(num1) + int(num2)
    elif operators == '-':
        result = int(num1) - int(num2)
    elif operators == '*':
        result = int(num1) * int(num2)    
    elif operators == '/':
        result = int(num1) / int(num2)        
    else:
        result = 0    
    
    data = {
        'type': 'FBV',
        'result': result,
    }
    
    # 3. 응답
    return Response(data)
    
```

- **이렇게 함수 기반 뷰를 만드는데, return에 Response를 사용한다. Response는 DRF에서 제공해주는 것을 사용한다. 여기에 딕셔너리 형태로 넣어주면 들어가게 된다.** 
- 계산기 코드를 생성하고 data라는 딕셔너리를 Response에 넣어주자. Response를 command로 클릭하면 처음으로 들어가야할 인자가 data이다.

* * *
- **그 다음으로는 urls.py가서 코드를 추가한다.**

```python
from posts.views import PostModelViewSet, calculator
...

# DRF routers 설정
router = routers.DefaultRouter()
router.register('posts', PostModelViewSet)

urlpatterns = [
    path('', include(router.urls)),
    path('admin/', admin.site.urls),
    # 계산기 기능
    path('calculator/', calculator, name='calculator'),
]
```

- **이렇게 추가해주기. 그리고나서 다시 postman에서 http://localhost:8000/calculator/ 이 url로 GET으로 날려보면,**

```terminal
{
    "type": "FBV",
    "result": 0
}
```

- **이런식으로 JSON 형태의 데이터를 응답해준다. 추가로, postmnan에서 Params인 파라미터에 num1과 num2, operators를 추가해주고 값을 넣고 요청을 날리게 되면,**

<img width="870" alt="image" src="https://user-images.githubusercontent.com/95380638/167813103-0083fa60-bddb-4536-a1b0-63b30dc759b8.png">

- 이렇게 result에 8이 출력되는 것을 확인할 수 있다. 여기서 신기한 점은 우리가 postman에서 http://localhost:8000/calculator/ 여기까지만 입력했는데 위의 캡처를 보면 -> 추가로 생겨났다. 파라미터의 경우 url에 쿼리스트링으로 들어가기 때문에 추가가 되는 것이다.
- 우리가 POST로 요청을 날릴 경우에는 이렇게 되지 않는다. 


- **여기까지가 함수 기반으로 API View를 작성하는 기본적인 코드 및 방법이다. 이렇게 하면 단순히 모델 데이터가 아닌, 우리가 직접 코드를 작성해서 원하는 정보들을 API로 요청했을 때 응답해줄 수 있다.**
  - **@api_view()는 api형태로 전환해주는 역할을 한다. command로 들어가 보자.** 
  - **그리고 기존 함수기반 View와 응답 코드가 다르다. 전에는 render를 사용해서 html 파일 자체를 응답했다. 지금은 Django REST Framework에서 제공해주는 Response로 응답을 하는데, 여기에 딕셔너리를 넣어주었다.**
  
* * *   
  
### liongram-api에 계산기 만들기 - 클래스 기반 API View
- 이제는 클래스 기반 View로 만들어보자.

```python
from rest_framework.views import APIView
...

# 계산기 기능 / 클래스 기반 API View
class CalculatorAPIView(APIView):
    def get(self, request):
        # 1. 데이터 확인
        num1 = request.GET.get('num1', 0)
        num2 = request.GET.get('num2', 0)
        operators = request.GET.get('operators')

        # 2. 계산
        if operators == '+':
            result = int(num1) + int(num2)
        elif operators == '-':
            result = int(num1) - int(num2)
        elif operators == '*':
            result = int(num1) * int(num2)    
        elif operators == '/':
            result = int(num1) / int(num2)        
        else:
            result = 0    
        
        data = {
            'type': 'CBV',
            'result': result,
        }
        
        # 3. 응답
        return Response(data)

```

- **먼저 기본적인 APIView를 Import 해준다. 이 APIView는 다른것들에 다 상속을 받는 건데, command로 보면 다양한 게 많지만 우리가 핵심으로 알아야 할 것은, 클래스 기반 View에서 GET 요청을 어떻게 처리할지가 중요하다.**
  - **이 때는 get이라는 함수를 만들어서 위의 계산기 기능을 동일하게 시켜주면 된다. 위의 코드를 가져와서 get 함수 내부에 위치시켜주자.**

- 그리고 urls.py에서 추가로,

```python
from posts.views import PostModelViewSet, calculator, CalculatorAPIView
...

urlpatterns = [
    path('', include(router.urls)),
    path('admin/', admin.site.urls),
    # 계산기 기능 / 함수 기반 API View
    # path('calculator/', calculator, name='calculator-fbv'),
    # 계산기 기능 / 클래스 기반 API View
    path('calculator/', CalculatorAPIView.as_view(), name='calculator-cbv'),
]
```

- **이렇게 CalculatorAPIView를 연결시켜준다. 그 다음에 postman으로 똑같은 url로 요청을 보내면 type이 CBV로 변한것을 확인할 수 있다.**

- 그리고 추가로 CalculatorAPIView 내부에 def post(self, request):로 하게 되면 -> POST 요청일 때를 적어줄 수 있다.
  - **여기에 항상 self가 들어가는 이유는, 클래스 내부에 있는 함수이기 때문에 self를 꼭 넣어줘야 한다. 그리고 지금은 두번째 인자로 request가 들어가야 한다.**

```python
# 계산기 기능 / 클래스 기반 API View    
class CalculatorAPIView(APIView):
    def get(self, request):
        # 1. 데이터 확인
        num1 = request.GET.get('num1', 0)
        num2 = request.GET.get('num2', 0)
        operators = request.GET.get('operators')

        # 2. 계산
        if operators == '+':
            result = int(num1) + int(num2)
        elif operators == '-':
            result = int(num1) - int(num2)
        elif operators == '*':
            result = int(num1) * int(num2)    
        elif operators == '/':
            result = int(num1) / int(num2)        
        else:
            result = 0    
        
        data = {
            'type': 'CBV',
            'result': result,
        }
        
        # 3. 응답
        return Response(data)
    

    def post(self, request):

        data = {
            'type': 'CBV',
            'method': 'POST',
            'result': 0,
        }
        
        # 3. 응답
        return Response(data)
```


- 이렇게 코드를 추가하고 Postman에서 POST로 날려보면 다르게 출력이 된다. 





