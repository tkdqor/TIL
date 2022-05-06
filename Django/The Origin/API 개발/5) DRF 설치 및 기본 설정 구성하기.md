## DRF 설치 및 기본 설정 구성하기
- 프로젝트를 하나 생성하고 시작. [해당 과정](https://github.com/tkdqor/TIL/blob/main/Django/The%20Origin/Prologue/Django%20%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0.md) 참고해서 진행하기.
- **그래서 Basic 폴더 내부에 liongram-api라는 새로운 프로젝트가 존재할 수 있도록 설정.**
  - **우리가 프로젝트를 새롭게 생성할 때 config라고 하는 이유는, 설정 파일들을 모아놓는 디렉터리라고 설정하기 위해서이다.**


## Django REST Framework 설치하기
- 새로 만든 liongram-api에 들어와있는 상태에서 DRF를 설치해보자.
- https://www.django-rest-framework.org/tutorial/quickstart/ 해당 공식문서에 나와있는 과정이다. 

```terminal
pip install djangorestframework 

pip3 install djangorestframework
```

- 먼저 설치를 해준다.(나는 pip3로 설치해야 오류가 나지 않았다.) 이 상태에서 pip list를 해보면 DRF가 추가되었다. 그리고나서 settings.py에 추가를 해줘야 한다.

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

- 즉, DRF도 외부 패키지라고 보면 된다. 


- **지금 가상환경이 어떤 경로인지 확인하려면, VSCode 메뉴에서 View - Command Palette - Python Select Interpreter - venv에 들어가서 python 선택.**

* * *
- 그리고 liongram-api 내부안에 새로운 App인 posts를 만들어준다. 

```python
django-admin startapp posts
```

### Serializer 만들기
- models.py를 키고 우리가 liongram의 posts App에서 했던 모델 코드들을 그대로 가져와보자.
- **그리고 posts라는 App안에다가 serializers.py라는 파일을 만들어준다. 그리고 해당 파일 안에다가 다음과 같은 코드를 추가해준다.**

```python





