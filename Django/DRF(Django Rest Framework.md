# DRF...

## DRF 기반의 API 서버를 만들기..?

1. 먼저 DRF 패키지 설치하기
```
pip install djangorestframework
```
터미널에 해당 명령어 입력



https://www.youtube.com/watch?v=ywJWbAF6txQ 이거 내용 일단 보기...


- DRF 즉, Django Rest Framework가 뭔지... 이 라이브러리를 왜 사용하는지...
- DRF 기반의 API 서버를 만든다..?
- DRF 기반으로 서버를 만드는 것과 이걸 안하는 것이 차이가 뭔지..?
- 시리얼라이저는 장고 모델 데이터를 JSON 타입으로 바꿔주는(직렬화) 코드
- 장고 모델 데이터를 JSON 타입으로 뿌려주면 API로 통신이 가능하며 내 데이터를 JSON으로 바꿔줄 수 있다..!


* * * 

- app 디렉토리 안에 serializers.py 파일 만들기
- views.py 안에다가 API를 만들기 (GET메소드로..)
- urls.py도 설정해서 브라우저를 통해 화면을 보면...
![image](https://user-images.githubusercontent.com/95380638/148948523-21eaae8d-3ce8-4a12-bf2d-534d83cd1f3d.png)
- 이렇게 DRF 화면이 나오게 된다. GET방식의 API가 나온다. 이런 방식으로 API를 개발하자..?
- views.py 에 추가로 다른 API도 만들어주자. 이 때 모델에 저장되어있는 정보들을 가져오게 된다.
![image](https://user-images.githubusercontent.com/95380638/148949980-155ccbdd-1ef0-41ee-8c4f-36b6f4219d29.png)
