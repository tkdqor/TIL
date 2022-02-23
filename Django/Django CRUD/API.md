## API
- **API란, Application Programming Interface의 약자로 응용 프로그램에서 사용할 수 있도록 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스.**
- ex) 우리가 지도 기능이 필요할 때, 직접 만들지 않더라도 다른 기업이나 정부가 만든 프로그램 혹은 라이브러리의 힘을 빌려서 이용할 수 있다. 다만, 회사가 만든 기술은 지적 자산이기 때문에 무료로 오픈할 이유는 전혀 없다.
  - 그러나, 카카오, 네이버, 구글 등은 자신들이 보유한 기술 중 일부를 대중에게 무료 혹은 유료로 오픈하고 있다. 그리고 해당 기술을 어떻게 사용하면 되는지 사용 가이드도 제공한다.

- 즉, 지도를 비롯해 날씨 정보, 음성인식 기술, 머신러닝 기술 등 각종 유용한 기능들을 활용하고 싶다면 -> 서비스 제공자가 사전에 정의하여 제공해주는 해당 가이드에 따라서 원하는 정보를 요청하기만 하면 된다.
  - **그래서 우리가 만든 애플리케이션과 - 이미 누군가가 만들어놓은 애플리케이션, 소프트웨어 사이를 연결해주는 하나의 약속된 규칙을 바로 interface라고 하는 것이다. 그리고 이 interface를 우리는 API라고 부른다.**
  - 우리가 각종 API를 활용하면 지도 등을 비롯해서 다양한 기능들을 쉽게 활용할 수 있다.

- **그리고 우리가 사용하게 될 대부분의 API는 HTTP를 기반으로 한다.**
  - **가이드에 따라서 HTTP Method request url을 정확히 입력해 데이터를 요청하기만 하면 된다. 그러면 API 제공자는 JSON이나 XML 등의 포맷으로 우리가 요청한 데이터에 대해서 HTTP Response를 내려준다.**
    - 그래서 일단, 우리가 원하는 기능을 직접 구현하기 어렵다면 해당 기능을 API로 제공하는 곳이 있는지 검색해보자.
    - 그래서 있다면, API 문서를 읽고 시키는 대로 요청을 보내보자.
    - 마지막으로 HTTP Response, 응답을 활용해서 서비스에 녹여내면 된다.


### dog API 사용 예시
- API는 항상 어떻게 요청하면 되는지 가이드를 제공한다. ~한 url pattern으로 요청을 주라고 한다던지 등 요청을 하면 어떠한 response를 내려줄지 나와있다. 
- **요즘 대부분의 API는 JSON 포맷으로 response를 내려주지만, 구형 API의 경우에는 XML의 포맷으로 내려주는 경우도 많다.**
  - **JSON 포맷이란 python의 딕셔너리와 거의 비슷한 자료 구조로 되어있다.**

- 모든 API가 자유롭게 사용할 수 있는 건 아니다. 특정 API는 호출량을 제한하고 호출 주체를 특정하기 위해서 서비스 가입자에게 특별한 키를 부여한 다음 해당 키를 사용해 호출한 주체를 판별하거나 호출량을 파악한다.

- 이러한 API를 사용하기 위해 우리는 -> HTTP Request를 전송하기만 하면 된다.
  - **python을 활용해서 HTTP Request를 전송할 수 있는 방법은 다양하지만, 가장 대중적인 방법인 Requests라는 라이브러리를 사용해보자.**

```terminal
python -m pip install requests
```

- 먼저 이렇게 requests를 설치해주자. 물론 가상환경을 활성화한 상태에서 설치한다.


- 그리고 우리가 네이버와 같은 웹 페이지에서 보는 광고들은, 같은 광고가 계속해서 뜨기보다는 다른 광고로 계속해서 바뀌게 된다. 그래서 우리는 dog API를 통해 얻을 수 있는 강아지 사진을 활용해서 랜덤하게 강아지 사진을 출력하는 것으로 광고 흉내를 내보도록 하자.

* * *
- **dog API는 /api/breeds/image/random 이라는 url로 GET 방식의 request를 날리면 -> JSON 형태로 "status"에는 성공 여부를, "message"에는 강아지 이미지의 웹 주소를 담아서 전달해준다.**

- 브라우저에 https://dog.ceo/dog-api/ 라고 입력해서 들어가보자. 그러면 화면 가운데에 
<img width="515" alt="image" src="https://user-images.githubusercontent.com/95380638/155272153-226f11fe-a97b-471f-bf20-013448ba9265.png">

- 다음과 같이 API에 대한 설명이 나와있다. 해당 주소로 요청을 하게되면 그에 대한 HTTP Response로 JSON 형태의 데이터를 전달받을 수 있다.
  - status라는 key에는 API의 성공여부를 담고있어서, 성공했을 때는 "success"라는 문자열 데이터가 담겨오게 된다.
  - 그 다음 message라는 key에는 우리가 요청한 강아지에 대한 이미지 데이터, 강아지 이미지의 웹 주소가 전달되는 것을 확인할 수 있다.
  - 가운데 Fetch 버튼을 누르게 되면, 그 때 마다 HTTP Request가 되어 다른 이미지가 뜨게 된다. 그리고 API 주소를 전체 복사해서 브라우저에 입력해보면 JSON 형태의 데이터가 출력됨을 확인할 수 있다. 
    - 해당 페이지를 계속 새로고침 해보면, 새로고침 할 때마다 message로 전달되는 강아지의 이미지 주소가 계속해서 바뀌는 것을 확인할 수 있다.

- 그럼 이제 우리의 requests 라이브러리로 이 dog api를 호출해보자.
- 보여주고 싶은 template과 연동된 View 함수 내부에서 이 API와 관련된 코드를 입력해야 한다. 게시글 목록 페이지에서 dog 이미지를 보여주려고 하기 때문에 지금은 index View에 코드를 작성해보자.

```python
import requests

# 게시판 목록 조회
def index(request):
    # dog API 활용 부분
    dog_api_response = requests.get('https://dog.ceo/api/breeds/image/random')    # 그냥 request가 아닌 라이브러리 requests
    dog_api_response_dictionary = dog_api_response.json()
    dog = None
    if dog_api_response_dictionary.get('status') == 'success':
        dog = dog_api_response_dictionary.get('message')


    posts = Post.objects.all()    # Post 모델 테이블 전체 데이터를 가져오기

    context = {
        'posts': posts,
        'dog': dog,
    }
    return render(request, 'posts/index.html', context)
```

- 우리는 API를 호출하기 위해서 requests 모듈 라이브러리를 사용할 것이니까 import requests를 먼저 해주자.
- **dog_api_response = requests.get('https://dog.ceo/api/breeds/image/random') -> 우리는 Index View 함수가 호출될 때마다 requests 모듈을 활용해서 API를 요청하게 된다.**
  - **requests.get()이라는 함수를 사용하면 -> GET 방식으로 API를 요청할 수 있다.** 첫번째 인자로, 요청할 API의 url를 넣어주기만 하면 된다. 우리가 요청할 API의 url은 https://dog.ceo/api/breeds/image/random 이니까 이걸 입력해주는 것이다.
  - 그리고 get 함수의 호출 결과를 dog_api_response라는 변수에다가 저장한다. 이 변수에는 -> HTTP Response에 대한 다양한 정보를 담게 된다. 여기에는 JSON 데이터 이외에도 해당 응답이 성공적인 응답인지 아닌지, 어떠한 포맷으로 전달되었는지 등 다양한 정보가 모두 담겨있다. -> 그래서 여기서는 우리가 실제 활용하고자 하는 JSON 데이터만 추출해서 사용해야 한다. 

- **만약 API의 응답이 JSON 형태로 전달되었다면 -> dog_api_response_dictionary = dog_api_response.json() 이렇게 .json() 함수를 통해서 해당 response를 JSON 형태로 변환해서 사용할 수 있게끔 만들어줄 수 있다. python으로 치면 딕셔너리 형태로 변환해서 사용할 수 있게 되는 것이다.**
  - 이렇게 변환된 결과를, python의 딕셔너리를 dog_api_response_dictionary라는 변수에 저장한 것이다.
  - 이제 dog_api_response_dictionary라는 변수에는 JSON 데이터가 python 딕셔너리로 변환이 되어서 들어가 있고, status가 success일 경우에만 message라는 key로 전달된 강아지 이미지 주소를 추출해서 이것을 활용해 template에서 사용하는 것이다.


- 그래서 dog = None이라는 코드를 작성해서, 어떤 값도 들어있지 않는 비어있는 변수를 만들자.

- 그리고 **if dog_api_response_dictionary.get('status') == 'success': -> 해당 딕셔너리에 status라는 key에 해당하는 값이 success라면, 즉 API가 성공적으로 응답되었다면** 
  - **dog = dog_api_response_dictionary.get('message') -> 딕셔너리에서 message라는 key에 저장되어있는 값, 강아지 이미지 주소 데이터를 추출해서 dog라는 변수에다가 덮어쓰기를 한 것이다.**

- **즉, dog라는 변수는 처음에는 None 이라는 값이 저장되어 있지만, API의 응답이 성공적으로 처리되었을 경우에는 강아지 이미지 주소가 들어있게끔 변경이 되는 것이다.**
  - 이 dog라는 변수를 context에 추가해서 index template에 보내준다.

* * *
- index template에서는 전달받은 강아지 이미지 데이터인 dog가 있을 경우, dog라는 변수를 활용해서 레이아웃을 구성하면 된다.
```html
<!-- dog API -->
{% if dog %}
<div class="card mt-3">
    <div class="row g-0">
        <div class="col-md-4">
            <img class="card-img" src="{{ dog }}" alt="Dog Image">
        </div>
        <div class="col-md-8 card-body">
            <h5 class="card-title">강아지를 보고 싶다면?</h5>
                <p class="card-text">Dog API를 사용하자!</p>
                <a href="https://dog.ceo/dog-api/" class="card-link" target="_blank">Dog API</a>
        </div>
    </div>
</div>
{% endif %}
```

- dog 변수가 있는 경우에만 -> img class="card-img" src="{{ dog }}" alt="Dog Image" 이렇게 src attribute에 dog 변수를 활용해서 이미지를 보여주게 된다.

- 이러면 이제 게시물 목록 하단에 강아지 이미지와 간단한 문구도 확인할 수 있다. 그리고 새로고침 할 때마다 매번 새롭게 dog API를 호출하면서 새로운 강아지 이미지를 불러오게 된다. 


* * *
### 카카오 우편번호 서비스 API
- 서비스의 가입자나 업체의 위치정보를 기반으로 서비스를 운영해야 하는 경우, 주소 체계와 좌표 체계를 다루는 것이 핵심인데 우리가 전국을 다 누비면서 건물 주소와 이름을 하나하나 다 수집할 수는 없다. 그래서 이와 같은 서비스를 이용하게 되면, 주소 검색 기능을 쉽게 이용할 수 있다.
- 해당 서비스는 카카오가 개발하여 제공하는 서비스이지만, 카카오도 전국을 다 뒤져서 주소 데이터베이스를 수집하고 주소 체계를 완성해서 이러한 서비스를 제공하는 것은 아닐 것이다.
  - 사실 카카오가 개발한 이 우편번호 서비스도 -> 행정안전부에서 제공하는 주소 데이터베이스를 받아서 일부 데이터를 보정하는 정도로만 포장해서 개발한 서비스이다.
  - 즉, 카카오 이전에 행정안전부, 정부 차원에서 관리하는 주소 DB가 있었고 이러한 주소 DB를 이용할 수 있게 해주는 API가 있다는 말이다.
 
- 그래서 우리도 행정안전부의 도로명주소 API를 활용하면 정부에서 관리하는 주소DB로부터 직접 데이터를 활용할 수 있다.


### 도로명주소 API 
- 구글에 도로명주소 API를 검색하고, 도로명주소 개발자센터라는 페이지를 클릭하자. 그러면 행정안전부가 제공하는 가이드 페이지로 이동할 수 있다. https://www.juso.go.kr/addrlink/devAddrLinkRequestGuide.do?menu=roadApi
- 이 API는 dog API와는 다르게 사용하기전에 반드시 신청을 해주어야 한다. 해당 페이지 좌측에 API 신청하기를 눌러서 -> API 종류는 도로명주소 API / API 유형은 검색 API / 업체와 url은 마음대로 적기. ex) sangbaek / www.sangbaek.com / 서비스망은 인터넷망 / 서비스용도는 개발을 선택. / 날짜는 넉넉하게 90일 선택.
  - 이렇게 개발용도로 사용할 경우에는, 제한된 기간내에서만 사용할 수 있다. 기간 제약 없이 장기적으로 운영하려면 실제 있는 업체 정보와 실제 있는 우리 서비스의 url 정보를 입력해서 운영용 키를 별도로 발급받아야 한다.
  - 이제 신청하기 버튼을 누르면, 개발 승인키는 재확인을 할 수 없다고 하면서 경고창이 뜬다. 그래서 승인키를 복사해두라고 한다. 
  - 확인을 누르면 신청내역이 뜨는데, 우리가 발급받은 승인키가 나온다. 이 키는 우리가 이 현재 페이지에서만 확인할 수 있고 이 페이지를 닫으면 다시 확인할 수 없게 된다.
    - 그리고 지금 발급받은 키는 도로명주소 검색 API 용도로만 사용할 수 있다고 나온다. 그래서 해당 키를 잊어버리지 말고 기록해두자.

- 우리가 다른 API를 사용할 때에도, 이렇게 키를 발급받는 절차가 있을 수 있는데 이러한 키는 오직 본인만 알고 있어야 한다. 일부 API는 호출량이 많아질 경우에 비용을 지불해야 하는 API가 있는데, 만약 이러한 키가 타인에게 유출 된다면 우리는 사용하지도 않았는데 계속해서 돈이 빠져나갈 수도 있기 때문이다.

- 그리고 이제 웹 페이지 좌측에, 오픈API - 도로명주소API를 클릭해보면 -> 팝업API와 검색API가 있는데, 우리는 이중에서 검색API를 사용해보자. 검색API에서 API정보/요청변수/출력결과보기 를 클릭하자.
  - 그러면 도로명주소 검색 API를 어떻게 사용하면 되는지 설명이 나온다. 먼저 호출방식은 POST방식과 GET방식 모두 다 사용할 수 있다고 나와있고 출력결과도 xml, json 중 우리가 원하는 형태를 선택해서 사용할 수 있다고 가이드에 나와있다. 
  - JSONP 방식과 일반 방식이 있는데, 일단 일반 방식을 사용해보자. 그래서 우리는 POST나 GET 방식으로 https://www.juso.go.kr/addrlink/addrLinkApi.do 라는 url로 요청을 날리면 된다.

- 해당 요청을 전송할 때, 어떠한 요청변수를 넣으면 되는지에 대한 설명이 추가로 가이드되고 있다. 어떠한 값을 반드시 넣어야 하는지, 값을 넣지 않았다면 어떤 값이 기본적으로 사용되는지에 대한 설명이 나와있다. 
  - 우리가 반드시 사용해야 하는 변수는 confmKey - 우리가 발급받는 승인키 정보를 같이 전송해줘야 한다. 그 다음은 keyword - 우리가 주소를 검색하는 거니까 어떤 주소를 검색하고 싶은지 검색어 정보를 keyword라는 데이터로 같이 전달해줘야 한다.
  - 이렇게 요청변수를 담아서 HTTP Request를 server에 전송하게 되면, 출력결과로, HTTP Response로 어떤 주소와 관련된 데이터들을 전달해주는지도 자세하게 나와있다.

- 해당 url인 https://www.juso.go.kr/addrlink/addrLinkApi.do를 그냥 브라우저에 입력해보면(GET방식도 가능하다고 했으니) XML 포맷의 형태로 검색되지 않았다는 내용이 출력된다.
  - **다음과 같이 전달하고자 하는 데이터를 HTML tag 비슷한 형태로 구성해서 전달하는 방식이 XML 방식이다.**
  - API 가이드에 나와있듯이, resultType이라는 요청변수의 기본값이 xml로 되어있고 이 변수를 JSON으로 지정해주면 JSON 형태의 HTTP Response를 전달받을 수 있다.
    - 실험으로 브라우저에 위에 url에다가 요청변수를 추가할 때에는, 즉 GET 방식에서 추가적인 요쳥변수, 파라미터를 전달할 때에는 -> url 맨 끝에다가 ?를 적고 부수적으로 전달하고자 하는 데이터인 파라미터 이름을 적으면 된다. 그리고 그 값을 =로 적어주면 된다. ex) https://www.juso.go.kr/addrlink/addrLinkApi.do?resultType=json

<img width="794" alt="image" src="https://user-images.githubusercontent.com/95380638/155287629-f6da9c96-9d47-43a1-b30d-0067c89e2e7a.png">

<img width="1204" alt="image" src="https://user-images.githubusercontent.com/95380638/155287660-1ac1494b-2a3b-4296-aaad-545d77f65554.png">

- 그러면 위와 같이 응답 데이터는 똑같은데, 포맷이 XML에서 JSON으로 변경된 걸 확인할 수 있다.

- 추가로, keyword라는 요청변수를 이용해서 검색어를 전달해보자. GET 방식에서 여러 개의 파라미터를 전달할 때는 ? 뒤에 첫번째 파라미터=값 적고 &를 적고 두번째 파라미터=값을 적으면 된다.      
  ex) https://www.juso.go.kr/addrlink/addrLinkApi.do?resultType=json&keyword=강서
  - 그런데, 이렇게 입력해서 보면 승인되지 않은 KEY라고 나온다. 그 이유는 이 도로명주소 API를 사용하기 위해서는, 반드시 승인키를 전달해줘야 하기 때문이다. 그래서 confmKey 요청변수에 승인키를 담고 다시 하자.     ex) https://www.juso.go.kr/addrlink/addrLinkApi.do?resultType=json&keyword=강서&confmKey=...

<img width="636" alt="image" src="https://user-images.githubusercontent.com/95380638/155288917-60270f67-b2f5-4913-9ca1-f14f28cbb45c.png">

- 이렇게 요청하게 되면 키워드로 검색한 다양한 주소 정보를 API response로 전달받을 수가 있다. 

- **추가로, 오픈API -> API활용체험을 눌러보면, 도로명주소 API / 영문주소 API / 상세주소 API / 지도제공 API 이렇게 다양한 API를 직접 체험해볼 수 있다.**


* * *
### 도로명주소 API 적용해보기
- 먼저, map이라는 새로운 app를 만들어보자.
```terminal
python manage.py startapp map
```

- 그리고 settings.py에 등록해주자. 또한, 프로젝트 디렉터리 내부 urls.py로 가서 url을 설정하자.
```python
urlpatterns = [
    ...
    # map으로 요청시 map 앱으로 연결
    path('map/', include('map.urls')), 
]
```

- 이렇게 map/ 으로 url 요청 시 -> map app에 있는 urls.py를 연결시킨다. 그리고 map 앱 내부에서 urls.py를 생성하고 설정하자.
```python
from django.urls import path
from . import views

app_name = 'map'

urlpatterns = [
    path('search/<str:keyword>/', views.search, name='search'),
]
```

- map/search/ 그 다음, 어떤 검색어를 입력할 경우에는 search라는 View 함수가 처리할 수 있도록 한다. 
  - search/ 이후에 오는 url를 keyword로 받되, type이 int가 아닌 str -> 즉, 문자열 형태의 keyword라는 변수로 받을 수 있게끔 설정한 것이다.

- 다음은 View 함수를 설정하자.
  - 우리가 지금까지는 View 함수를 설정할 때, 최종적인 HTTP Response로 단순한 텍스트를 주거나 HTML를 주는 방식으로 개발했었다. 그런데 이번에는 JSON 형태로 응답하는 API를 개발해 볼 것이다.
```python
from django.shortcuts import render
from django.http import JsonResponse

# Create your views here.

def search(request, keyword):

    return JsonResponse({'name': 'sangbaek', 'age': 29})
```    


- 그래서 우리는 from django.http import JsonResponse 이렇게 JsonResponse를 import 해주자. 일단 위와 같이 search 함수의 return를 JsonResponse으로 주고 서버를 실행해보자.
  - 그리고나서 브라우저에 http://localhost:8000/map/search/분당 이렇게 검색해보면, {"name": "sangbaek", "age": 29} 이렇게 우리가 View 함수에서 작성한 JSON 형태가 출력된다.
  - **즉, 우리가 View 함수에서 작성한 딕셔너리 데이터가 JSON 형태로 변환이 되어서 HTTP Response로 전달되는 걸 확인할 수 있다.**


- **이제 사용자가 입력한 값에 따라서 다른 데이터를 Response 하고 싶다면, keyword라는 변수를 활용해야 한다.**
```python
from django.shortcuts import render
from django.http import JsonResponse

# Create your views here.

def search(request, keyword):

    return JsonResponse({'keyword': keyword})   # python 딕셔너리 데이터를 JSON 형태로 전환해서 HTTP Response를 해준다.
```

- 위와같이 입력하고 url 요청을 하면 {"keyword": "\ubd84\ub2f9"} 이렇게 한글 자체가 깨져서 출력된다. 
  - django의 JsonResponse 함수가 주어진 데이터를 활용해 JSON 형태의 문자열을 구성하는 과정에서 ASCII 인코딩이 아닌 문자열들을 자동으로 변환해버리는 이슈가 있다.
  - 따라서, 이러한 django의 처리 방식을 수정해서 한글은 ASCII 인코딩으로 표현할 수 없는 문자이기에, 한글이 포함된 JsonResponse를 만들고 싶다면 JsonResponse 함수를 사용할 때 -> 

```python
def search(request, keyword):

    return JsonResponse({'keyword': keyword}, json_dumps_params={'ensure_ascii': False})   
```

- 다음과 같이 json_dumps_params라는 옵션에 ensure_ascii를 False로 지정해서 옵션을 추가해주면 정상적으로 한글이 출력된다.
<img width="375" alt="image" src="https://user-images.githubusercontent.com/95380638/155295140-ebcc5b09-8dfd-4c96-8c32-2da561615b1b.png">

- 그래서 이렇게 입력된 키워드가 그대로 JSON 형태로 출력해줄 수 있게 된다.

* * *
- **이제 강남, 분당 이러한 keyword를 전달받아서 도로명주소 검색 API를 대신 호출해줄 수 있도록 하자. 그리고 그 호출결과를 우리가 보는 페이지에서 볼 수 있게끔 구현해보자.**

- 먼저 HTTP Request를 만들기 위해서 map App의 views.py에서 requests 모듈을 import 하기.
```python
from django.shortcuts import render
from django.http import JsonResponse
import requests

# Create your views here.

def search(request, keyword):
    result = requests.post('https://www.juso.go.kr/addrlink/addrLinkApi.do', {    # 해당 url로 POST 방식으로 HTTP Request를 진행
        'confmKey': 'devU01TX0FVVEgyMDIyMDIyMzE3MTYwOTExMjI3MzM=',                # 이 떄, 요청변수를 딕셔너리 형태로 넣어서 요청하기
        'keyword': keyword,                                                       # keyword는 사용자가 입력한 값을 의미
        'resultType': 'json',
    })


    return JsonResponse(result.json(), json_dumps_params={'ensure_ascii': False}) # JsonResponse의 첫번째 인자로, Http Response를 json 형태로 바꾼 값을 넣어주기
    # python 딕셔너리 데이터를 JSON 형태로 전환해서 HTTP Response를 해준다.
    # json_dumps_params 라는 옵션으로 JSON에서 한글 출력이 가능하도록 설정.
```

- 그리고 search라는 View 함수가 실행될 때마다 requests.get() 함수를 통해서 GET 방식으로 요청할 수도 있지만, 이번에는 POST 방식으로 요청해보자. requests.post() 함수를 사용하면 된다.
  - 첫번째 인자로는 API의 url를 적어주면 되니까 https://www.juso.go.kr/addrlink/addrLinkApi.do를 적어준다.
  - 두번째 인자로는 POST 방식이니까 request body로 보낼 데이터를 딕셔너리 형태로 적어주면 된다. 
    - ex) {
        'confmKey': 'devU01TX0FVVEgyMDIyMDIyMzE3MTYwOTExMjI3MzM=',
        'keyword': keyword,
        'resultType': 'json',}
    - 이렇게 요청변수와 값을 입력해주자. 여기서 keyword라는 변수의 값인 keyword는 사용자가 입력한 값에 따라서 그 값을 그대로 전달받아 활용해주는 것이다.
    
- result = requests.post('https://www.juso.go.kr/addrlink/addrLinkApi.do', ... -> 이렇게 requests.post() 함수의 결과를 result라는 변수에 저장해주고.      
  result.json() -> 이렇게 응답결과를 python 딕셔너리로 변환해서 사용할 수 있게 하자. 그래서 이 result.json()를 JsonResponse 함수의 첫번째 인자로 넣어준다.
  
- **이제, 브라우저에서 localhost:8000/map/search/강남/ 이런식으로 url을 입력하면 자동으로 강남 이라는 키워드로 검색한 결과를 json 형태로 보여주게 된다.**
  - **이러한 실습으로 도로명주소 검색 API처럼 우리도 직접 JSON 형태의 response를 내려주는 API서버를 개발할 수 있다는 것을 알 수 있다.**
  - 그리고 도로명주소 검색 API를 활용한 것처럼 view 함수 내부에서 requests 모듈을 사용해 외부 API를 필요할 때마다 사용할 수 있다. 
  - 마지막으로 우리가 search view 함수를 작성하면서 도로명주소 검색 API의 응답 결과를 그냥 그대로 전달만 했지만 -> 복잡한 응답 결과 속에서 우리가 필요한 데이터만 추출해서 가공을 하면,     
    예를 들어 실제 도로명 주소와 해당 주소의 영어 주소, 이 2개를 추출해서 구성한다면 -> 우리가 분당이라고 입력했을 때, 그 분당이라는 keyword를 포함하고 있는 도로명주소와 영어주소만 포함하고 있는 **우리만의 API를 개발할 수도 있다.**
    
- **그래서 여기까지, 다른 사람들이 제공하는 API를 활용해보고 / 우리가 직접 API를 제공하는 주체, API서버를 만들어보기도 했다.**
