# DRF
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
1. 먼저 DRF 패키지 설치하기
```
pip install djangorestframework
```
터미널에 해당 명령어 입력

2. 프로젝트 디렉토리 안에 있는 settings.py에서 
```python
INSTALLED_APPS = [
    ...,
    'rest_framework',
]
```
새로운 앱으로 추가해주기




https://www.youtube.com/watch?v=ywJWbAF6txQ 이거 내용 일단 보기...


- DRF 즉, Django Rest Framework가 뭔지... 이 라이브러리를 왜 사용하는지...
- DRF 기반의 API 서버를 만든다..?
- DRF 기반으로 서버를 만드는 것과 이걸 안하는 것이 차이가 뭔지..?
- 시리얼라이저는 장고 모델 데이터를 JSON 타입으로 바꿔주는(직렬화) 코드
- 장고 모델 데이터를 JSON 타입으로 뿌려주면 API로 통신이 가능하며 내 데이터를 JSON으로 바꿔줄 수 있다..!
- 개인 프로젝트 시작할 때 -> 먼저 DRF 라이브러리 설치하고 시작..?

==> 반환을 할 때, JSON형태로 응답... / 그걸 프론트엔드에서 요청할 때 서버에서 응답을해주는데 이걸 RESTFUL하게 해준다..는 것...
- 파이썬으로 로컬 서버를 열 때, 그냥 하려면 힘듬...일일이.. —> 프레임워크를 실행만 하면 서버를 가동할 수 있게끔..
- 시리얼라이저는 장고 모델 데이터를 JSON 타입으로 바꿔주는(직렬화) 코드
- 장고 모델 데이터를 JSON 타입으로 뿌려주면 API로 통신이 가능하며 내 데이터를 JSON으로 바꿔줄 수 있다..!
- JSON형태의 결과값은 문자열, string으로 인식된다. 그래서 받은 입장에서 가공을 해줘야 한다.

==> 장고 서버를 만든 것을 -> 라이브러리를 설치해서 Restful한 API서버로 만들었다는 것이다!

* * * 

- app 디렉토리 안에 serializers.py 파일 만들기
- views.py 안에다가 API를 만들기 (GET메소드로..)
- urls.py도 설정해서 브라우저를 통해 화면을 보면...
![image](https://user-images.githubusercontent.com/95380638/148948523-21eaae8d-3ce8-4a12-bf2d-534d83cd1f3d.png)
- 이렇게 DRF 화면이 나오게 된다. GET방식의 API가 나온다. 이런 방식으로 API를 개발하자..?
- views.py 에 추가로 다른 API도 만들어주자. 이 때 모델에 저장되어있는 정보들을 가져오게 된다.
![image](https://user-images.githubusercontent.com/95380638/148949980-155ccbdd-1ef0-41ee-8c4f-36b6f4219d29.png)
