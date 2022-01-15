# DRF(Django Rest Framework)
- DRF는 Django에서 RestFul한 API 서버를 구축하기 위한 라이브러리
- RestFul API에 대한 설명은 다음을 참고 https://velog.io/@somday/RESTful-API-%EC%9D%B4%EB%9E%80
- RestFul API란, HTTP 메소드를 통해 정해진 통신 규격에 맞게 어떤 종류의 Request를 할지 명시적으로 알 수 있게끔 해준다.


## DRF 기반의 API 서버를 만들기
- **DRF 기반의 API 서버란?**
  - Django를 이용하여 개발한 서버에서 클라이언트가 HTTP 메소드를 이용해 Request를 했을 때, 원하는 정보를 정해진 형태(JSON 등)로 Response 해줄 수 있는 서버를 구축하는 것을 의미. 
  - 즉, api서버를 구성했다는 것은 클라이언트에서 원하는 정보를 전달해줄 수 있는 서버를 구현한다고 생각하면 된다.
  - DRF 기반의 API 서버를 구축하지 않는 경우, 보통 클라이언트의 Request에 대해서 웹 페이지를 랜더링 해주게 된다. 그리고 Request한 API에 대해 정보를 전달해주지는 않는다.

* * *

## DRF 실습 해보기
**1. 먼저 DRF 패키지 설치하기**
```
pip install djangorestframework
```
터미널에 해당 명령어 입력


**2. 프로젝트 디렉토리 안에 있는 settings.py에서** 
```python
INSTALLED_APPS = [
    ...,
    'rest_framework',
]
```
새로운 앱으로 추가해주기


**3. Serializer 생성하기**
- app 내부 디렉토리안에 serializer.py 생성하기
```python
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ('product_name', 'brand', 'created_at')
```
- serializer는 장고 모델 데이터를 JSON 타입으로 바꿔주는(직렬화) 코드이다.
- serializers와 app에 설정한 모델(여기서는 예시로 Post)을 import 해준다.
- 클래스는 "모델이름Serializer" 라고 설정 -> 이 때, ModelSerializer 클래스를 활용하게 되면 Model에 정의한 필드에 해당하는 값을 Serializer에서 사용할 수 있게 된다.
- 위에 코드에서는 Post라는 모델을 사용하고 fields는 사용하고 싶은 필드(column)를 나열해주면 된다. __all__를 입력할 경우 모든 필드를 사용하곘다는 의미이다. 


**4. views.py에서 API 설정해주기**
```python
from rest_framework.response import Response
from rest_framework.decorators import api_view
from .serializers import PostSerializer


# RDF를 이용한 상품 정보 1개에 대한 API
@api_view(['GET'])
def Postinfo(request, post_id):
    post = Post.objects.get(id=post_id)
    serializer = PostSerializer(post)

    return Response(serializer.data)
```
- GET메소드로 API를 설정하기
- 요청되는 request에 대한 정보와 DB에 저장되어있는 상품 1개에 대한 정보를 받기 위해 request와 post_id를 파라미터로 설정
- post라는 임의의 변수에 Post모델 1개 데이터를 담기
- serializer라는 변수에 serializers.py에 설정해둔 PostSerializer를 사용하기(괄호안에는 모델이름 설정)
- 마지막으로 serializer.data로 반환해주면 된다.


**5. urls.py에서 url 설정해주기**
```python
from django.urls import path
from . import views
from .views import helloAPI, Postinfo

app_name = 'posts'
urlpatterns = [
    ...,
    path('<int:post_id>/postinfo', Postinfo),
]
```
- 이런식으로 views.py에 있는 Postinfo 함수를 import하고 post_id를 전달할 수 있는 url를 설정하면 된다.


**6. 그리고 해당 url로 request를 해보면,**
![image](https://user-images.githubusercontent.com/95380638/149622616-d0e6631a-ca45-4186-8302-8eb38ad4a83e.png)
- 이런식으로 post_id에 해당하는 Post모델에 데이터를 JSON 형태로 전달해준다.

* * * 
## DRF 기반으로 API 서버를 구축하는 이유?
=> **프론트엔드와 백엔드의 분리가 가능해진다.**

- 프론트엔드 개발자는 서버로 Request를 하면 JSON 형태의 데이터를 얻을 수 있기 때문에 django와 관련된 내용을 알고 있지 않아도, Serialization된 JSON 형식의 데이터를 API 서버로부터 받아서 개발할 수 있다.
  - JSON 형태의 결과값은 문자열, string으로 인식되기 때문에 받는 입장에서는 가공을 해줘야 한다.
- 백엔드 개발자는 API 서버를 개발하는 것에 집중하여 각각 독립된 개발환경을 유지할 수 있다. 

