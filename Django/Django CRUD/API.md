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
  - **JSON 포맷이란 python의 딕셔너리와 거의 비슷한 자료 구조로 되어있다. **

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
