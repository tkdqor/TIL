# Distribute

📖 **Contents**

- Distribute
  - [AWS EC2 ssh 연결하기](#aws-ec2-ssh-연결하기)
  - [Collectstatic 명령어](#collectstatic-명령어)
  - [CI란](#ci란)
  - [TDD란](#tdd란)
  - [pre-commit이란](#pre-commit이란)
  - [Formatter란](#formatter란)
  - [linter란](#linter란)
  - [컴파일이란](#컴파일이란)
  - [빌드란](#빌드란)
  - [디펜던시란](#디펜던시란)
  - [MSA란](#msa란)
  - [unittest 예시](#unittest-예시)
  - [pytest 예시](#pytest-예시)
  - [Github Action이란](#github-action이란)
  - [Docker란](#docker란)
  - [Docker 및 Docker-compose 예시](#docker-및-docker-compose-예시)
  - [Docker 이미지와 컨테이너 차이](#docker-이미지와-컨테이너-차이)
  - [컨테이너와 가상머신의 차이](#컨테이너와-가상머신의-차이)


* * *

## AWS EC2 ssh 연결하기
- AWS EC2 화면에서 접속하고자 하는 EC2를 체크하고 "연결" 버튼 누르기
- 그리고 터미널에서 pem키가 있는 디렉터리로 이동 (ex. cd Downloads)

```terminal
chmod 400 impact-redis.pem
```

- 다음과 같은 명령어 입력. **chmod는 순서대로 나/그룹/전체에 대한 권한을 의미한다. 지금은 나에게만 읽기 권한을 주는 것이다.**

```terminal
ssh -i "impact-redis.pem" ubuntu@ec2-13-209-43-88.ap-northeast-2.compute.amazonaws.com
```

- 그 다음에는 위와 같은 명령어를 입력해서 해당 EC2에 ssh로 접속하기

* * *

## Collectstatic 명령어
- `python manage.py collectstatic` 명령어는 Django 프로젝트의 여러 app에서 사용하는 static 파일을 한 곳(경로)으로 모아주는 역할을 한다.

- **이렇게 static 파일들을 한 곳으로 모으는 이유는, CSS, Javascript, image 파일들로 이루어진 static 파일을 웹어플리케이션인 Django가 처리하기에는 너무 비효율적이기 때문이다.** 웹서버에서 웹 애플리케이션으로 요청을 전달하는 데는 WSGI라는 변환과정을 거치며 이것은 자원을 소모하는 일이고, 굳이 웹서버도 할 수 있는 일을 웹 애플리케이션에서 할 필요가 없는 것이다. 그래서 Django에서는 Production에서의 static 파일을 처리하는 기능을 담고있지 않다.
  - 물론 Django 개발할 때는 static 파일이 잘 전송되는 것을 볼 수 있다. 하지만 이것은 INSTALLED_APPS의 django.contrib.staticfiles이라는 모듈이 담당했던 것이고 settings.py의 DEBUG 속성을 False로 바꾸면 동작하지 않게된다. 이때부터는 static 파일의 처리는 웹서버가 담당하게 된다.

- [참고 블로그](https://crynut84.github.io/2016/11/14/django-static-file/)

* * *

## CI란
- CI란, Continuous Integration 즉, 지속적인 통합이라는 의미를 가지고 있다.
  - CI 시스템을 구축하게 되면 -> CI 서버는 형상관리 서버에 commit된 소스코드를 주기적으로 폴링하여 컴파일, 단위테스트, 코드 인스펙션 등의 과정을 수행하며 신규 또는 수정된 소스코드가 결함이 있는지 여부를 지속적으로 검토해준다. 이를 통해 조기에 결함을 발견하여 해결할 수 있다.
    - CI 서버는 빌드 프로세스를 관리하는 서버로 Jenkins가 여기에 속한다.
    - SCM, 즉 소스코드 형상 관리 시스템은 Git이 대표적이다. 

- **빌드 스크립트를 통한 CI 자동화 수행 절차**
  - 소스코드를 바이너리 파일로 컴파일
  - 바이너리 파일을 배포 형태로 패키징
  - 단위 테스트 수행
  - 정적 분석 수행
  - 분석 결과 리포팅
  - 패키징한 파일을 테스트 서버에 배포

- 터미널에 테스트 커맨드를 CI에 입력해두면 → 푸쉬를 바탕으로 디펜던시 설치 후 커맨드 자체를 입력하게 되어 test.py 실행시키는 명령어가 실행된다.
- Github 자체 로컬 공간에 환경을 구성해서 우리의 코드를 실행하게 된다.

* * *

## TDD란
- **Test Driven Development의 약자로 '테스트 주도 개발'이라고 한다.**
- TDD와 일반적인 개발 방식의 가장 큰 차이점은 테스트 코드를 작성한 뒤에 실제 코드를 작성한다는 점이다. 디자인(설계) 단계에서 프로그래밍 목적을 반드시 미리 정의해야만 하고, 무엇보다 어떤 테스트해야 할지 미리 정의(테스트 케이스 작성)해야만 한다.
  - 그래서, 테스트가 통과된 코드만을 코드 개발 단계에서 실제 코드로 작성하게 되는 개발 방식이다.

- **TDD의 장점**
  - TDD를 통한 소프트웨어 개발 시 기능 별 철저한 모듈화가 되어 보다 효율적인 객체 지향적인 코드를 생산할 수 있다.
  - 테스트 코드를 먼저 작성하기 때문에 개발자가 지금 무엇을 해야하는지 분명히 정의하고 개발을 시작할 수 있다.
  - 디버깅 시간도 단축된다. 예를 들면 사용자의 데이터가 잘못 나온다면 DB의 문제인지, 비즈니스 레이어의 문제인지 UI의 문제인지 실제 모든 레이어들을 전부 디버깅 해야하지만, TDD의 경우 자동화 된 유닛 테스팅을 전제하므로 특정 버그를 빠르게 찾을 수 있다.

- [관련 블로그](https://velog.io/@jsb100800/CS-%EC%8A%A4%ED%84%B0%EB%94%94-%EA%B0%9C%EB%B0%9C-%EC%A7%80%EC%8B%9D-TDD%EC%99%80-DDD)

* * *

## pre-commit이란
- **pre-commit 라이브러리란, 해당 라이브러리를 설치하면 commit 메세지를 작성하기 전에 호출을 해서 문법 오류나 코드 컨벤션 등을 체크할 때 사용한다.**
```terminal
# 설치 예시
git init .

pip install pre-commit

pre-commit install

# 만약, github hook를 사용한다면,
pre-commit install           # 해당 명령어 입력 시, 스크립트에 짜여진대로 hook이 .git/hooks/pre-commit에 생성

pre-commit installedat .git/hooks/pre-commit    # 생성한 훅을 사용한다는 명령어 입력 


# 설치 후 실행 예시
git add .

pre-commit run

# 이상이 없으면
git commit
```
- **위와 같이 pre-commit 라이브러리를 설치할 수 있다. 그리고 설치 후에는 git add . 이렇게 staging 영역에 올린 후, pre-commit run 명령어로 실행시킬 수 있다.**
  - pre-commit run 명령어 입력 전에, 루트 디렉터리 내부에 .pre-commit-config.yaml라는 파일을 생성해서 설정해놓아야 한다.
  - 또한, pre-commit 라이브러리로 git hook를 사용할 수 있다. [해당 블로그](https://www.daleseo.com/pre-commit/)를 참고하자.

```yaml
repos:
  - repo: https://github.com/PyCQA/isort
    rev: 5.10.1
    hooks:
      - id: isort
        exclude: ^.*\b(migrations)\b.*$

  - repo: https://github.com/ambv/black
    rev: 22.6.0
    hooks:
      - id: black
        exclude: ^.*\b(migrations)\b.*$

  - repo: https://github.com/pycqa/flake8
    rev: 4.0.1
    hooks:
      - id: flake8
        exclude: ^.*\b(migrations)\b.*$
```

- 위의 코드는 isort, black, flake8 적용 예시이다. rev에서 원하는 버전을 선택할 수 있다.

- [관련 블로그](https://daco2020.tistory.com/291?category=996085)

* * *

## Formatter란
- **Formatter란, 코드 스타일, 코드 컨벤션을 바로 잡아주는 툴을 의미한다.**

- **black**
  - black은 대표적인 코드 포멧터이다. 코드 스타일을 통일시켜 준다. PEP8을 기반으로 가독성이 더 좋은 코드스타일로 자동으로 변환해주는 포맷터이다.
  - [사용 예시](https://velog.io/@gyuseok-dev/Python.-Black-the-Code-Formatter)

- **isort**
  - isort란, python에서 import하는 라이브러리들을 자동으로 정렬해주는 라이브러리이다. import를 알파벳순, 유형별, 섹션별로 자동으로 정렬해준다.
  - [isort 설치](https://pypi.org/project/isort/)

- **black과 isort는 pyproject.toml 하나의 파일로 설정을 동시에 할 수 있기에 프로젝트 진행 시 해당 파일에 설정했다.**
  - migrations와 gitignore등은 skip 처리 진행

* * *

## linter란
- **linter란, 문법 오류들을 잡아주고 더 나은 코드를 제안해주는 검사기 툴을 의미한다.**
- 위의 설명된 Formatter와 같이, 이상적으로는 Formatter로 코드 컨벤션을 통일하고 -> linter를 활용해서 발생할 수 있는 에러를 수정하거나 더 나은 코드로 수정하는 방향이다.

- **flake8**
  - flake8은 코드 linter로 PEP8 규약을 지켰는지 검사해준다. black과 유사하나, 자동변환 기능은 없고 체크만 할 수 있습니다.
  - flake8은 자동으로 코드를 수정해주지는 않으니 직접 수정을 해야한다.
  - [flake8 설치](https://dev-yakuza.posstree.com/ko/django/flake8/)

- **위의 Formatter와 linter라는 두가지의 툴이 한쪽에서 체크가 불가능한 것이 다른쪽에서 체크가 가능하듯 상호보완적 관계가 있기에 같이 사용할 수 있다.**

- **flake는 .flake8파일에 설정할 수 있기에 프로젝트 진행 시 해당 파일에 설정했다.**
  - exclude설정으로 깃 관련, 마이그레이션, 캐시는 체크를 하지 않게 설정

* * *

## 컴파일이란
- 컴퓨터가 이해할 수 있는 기계어로 변환해주는 과정
- [파이썬의 동작 과정 참고](https://github.com/tkdqor/TIL/blob/main/%EA%B8%B0%EC%88%A0%20%EB%A9%B4%EC%A0%91%20%EB%8C%80%EB%B9%84%20%EA%B8%B0%EB%B3%B8%20%EA%B0%9C%EB%85%90%20%EC%A0%95%EB%A6%AC/Python.md#python%EC%9D%98-%EB%8F%99%EC%9E%91%EC%9B%90%EB%A6%AC)


* * *

## 빌드란
- 소스 코드 파일을 컴퓨터에서 실행할 수 있는 독립적인 형태로 변환하는 과정과 그 결과
- 즉, 컴파일 된 코드를 실제 실행할 수 있는 상태로 만드는 일을 Build 라고 한다. 
  - 빌드 다음에 “테스트" ⇒ 그 다음에 “배포" 과정이 진행된다.


* * *

## 디펜던시란
- **Dependency란, 의존성을 의미하는데 이는 "두 개의 컴포넌트 사이의 의존성이란 하나의 컴포넌트의 변경 사항이 다른 컴포넌트에도 영향을 미칠 가능성" 을 의미한다.**
  - 프로젝트를 진행할 때, 사용되는 패키지나 라이브러리의 version을 명시해두는 것을 의미한다. 이렇게 파일로 version을 명시해두고 패키지를 각자 설치할 때 달라지는 version를 일치시킬 수 있다.
    - pipenv에서는 git pull 이후에 pipenv install로 version 일치시키기
    - pip에서는 requirements.txt를 생성해서 dependency 충돌을 피할 수 있음


* * *

## MSA란
- **기존에는 Monolithic Architecture의 구조로, 소프트웨어의 모든 구성요소가 한 프로젝트에 통합되어 있는 형태였다.** 
  - 웹 개발을 예로 들면 웹 프로그램을 개발하기 위해 모듈별로 개발을 하고, 개발이 완료된 웹 어플리케이션을 하나의 결과물로 패키징하여 배포되는 형태이다.
  - 하지만 일정 규모 이상의 서비스, 혹은 수백명 이상의 개발자가 투입되는 프로젝트에서 Monolithic Architecture는 한계를 보인다.
  - 또한, 최근 어플리케이션 개발 방법은 CI/CD를 통한 개발부터 배포까지 빠르게 반영하는 추세인데, 많은 사람이 하나의 시스템을 개발하여 배포하기 때문에 시간이 오래 걸린다.

### MSA 개념
- **MicroService Architecture의 줄임말을 의미.**
  - 서비스를 마이크로하게 나눠서 관리하는 아키텍쳐를 의미한다.
  - 마이크로서비스란 작고, 독립적으로 배포 가능한 각각의 기능을 수행하는 서비스로 구성된 프레임워크라고 할 수 있다.
  - 마이크로 서비스는 서비스의 end-point(접근점)을 API 형태로 외부에 노출하고, 실질적인 세부 사항은 모두 추상화한다. 내부의 구현 로직, 아키텍처와 프로그래밍 언어, 데이터베이스, 품질 유지 체계와 같은 기술적인 사항들은 서비스 API에 의해 철저하게 가려진다.
  - ex) 카카오톡의 경우, 채팅기능, 선물하기 등 다양한 기능들이 있는데 이걸 하나의 서버가 아닌 기능별로 다른 서버에 분배해서 관리한다.

### MSA 장점
- 각각의 서비스를 모듈화할 수 있다. 이러한 MSA는 각각 개별의 서비스 개발을 빠르게 하며, 유지보수도 쉽게할 수 있도록 한다. 
- 팀 단위로 적절한 수준에서 기술 스택을 다르게 가져갈 수 있다. 회사가 java의 spring 기반이라도 MSA를 적용하면 node.js로 블록체인 개발 모듈을 연동함에 무리가 없다. 
- 마이크로서비스는 서비스별로 독립적 배포가 가능하다. 따라서 지속적인 배포 CD도 모놀로식에 비해서 가볍게 할 수 있다.

### MSA 단점
- MSA는 모놀리식에 비해 상대적으로 많이 복잡하다. 서비스가 모두 분산되어 있기 때문에 개발자는 내부 시스템의 통신을 어떻게 가져가야 할지 정해야한다. 또한, 통신의 장애와 서버의 부하 등이 있을 경우 어떻게 transaction을 유지할지 결정하고 구현해야한다. 
- 통합 테스트가 어렵다. 개발 환경과 실제 운영환경을 동일하게 가져가는 것이 쉽지 않다. 

- [관련 블로그](https://wooaoe.tistory.com/57)

* * *

## unittest 예시
```python
import unittest                               # unittest 라이브러리 import
from django.test import TestCase, Client      # Client를 이용해서 클라이언트 Request의 응답을 확인할 수 있다

class SampleTests(unittest.TestCase):         # TestClass 만들고 unittest.TestCase class에서 상속

# setUpClass와 tearDownClass은 Class 생성과 소멸 시 호출되는 함수
@classmethod
    def setUpClass(cls):  
    ...
    

@classmethod
    def tearDownClass(cls):
    

# Testcast에 대한 Texture 함수인 setUp(self)와 tearDown(self)를 구현  
# setUp(self)와 tearDown(self) 함수는 객체의 값을 self로 통해서 접근 가능 / 각 TestCase 함수가 실행할 때마다 앞뒤로 호출

# 여기서 우리가 정한 모델의 객체들을 샘플로 몇 개 create 하기 -> setUp에서 필요하고 실험할 변수들을 미리 생성해놓는 더미데이터 작업 진행
def setUp(self):
  self.client = Client()
  ...


def setUp(self):
  ...


# Testcase 함수를 구현 / 함수 이름이 test_ 로 시작하면 TestRunner가 알아서 실행

# 여기서 Serializer 내부에 정의한 메소드들을 테스트
# ex) 우리가 위에서 모델들의 객체들을 생성해 놨으니, 그 객체 하나를 objects.filter로 해서 가져오고, 실제로 Serialzer에 객체를 넣은 결과값과 vs 우리가 정의한 필드명 리스트와 같은지 비교

def test_runs_1(self):
  ...

def test_runs_2(self):
  ...


# ApiView의 GET 요청 test case
def test_rain_fall_and_sewer_pipe_api_view_get_method(self):
        '''RainfallAndSewerPipeInfoApiView으로 GET 요청 TEST'''

        response = self.client.get('/api/data/v1/rainfall-and-sewerpipe-info/01/202112020112/')
        response_json = json.loads(response.content)

        self.assertEqual(response.status_code, 200)
        self.assertNotEqual(response_json['name'], '양천구')
        self.assertEqual(len(response_json['rainfall_data']), 2)
        self.assertEqual(len(response_json['sewer_pipe_data']), 2)


# 마지막으로 unittest.main() 함수를 호출하여 TestRunner를 실행
# 터미널에 python3 디렉터리/tests.py 이런식으로 입력해서 테스트 결과 확인
if __name__ == '__main__':
    unittest.main()
    
```

- [관련 블로그](https://live-jh.tistory.com/44)


- **다른 unittest 예시**
```python
import json, requests, jwt

from django.test   import TestCase, Client
from unittest.mock import patch, MagicMock

from .models     import User
from my_settings import SECRET_KEY, ALGORITHM

class UserTest(TestCase):
    def setUp(self):
        User.objects.create(
            id       = 1,
            kakao_id = 123456789,
            email    = '123456789@gmail.com',
            point    = 10000000
        )

    def tearDown(self):
        User.objects.all().delete()

    @patch("users.views.requests")
    def test_kakaologinview_get_success(self, mocked_requests):
        client = Client()

        class MockedResponse:
            def json(self):
                return {
                    "id":123456789,
                    "kakao_account": {
                        "email" : "123456789@gmail.com"
                        }
                    }

        mocked_requests.get = MagicMock(return_value = MockedResponse())
        headers             = {"HTTP_Authorization": "3242343242343234"}
        response            = client.get("/users/login", headers)
        access_token        = jwt.encode({'user_id': 1}, SECRET_KEY, ALGORITHM)

        self.assertEqual(response.status_code, 201)
        self.assertEqual(response.json(), {'access_token': access_token})

    @patch("users.views.requests")
    def test_kakaologinview_get_keyerror(self, mocked_requests):
        client = Client()


        class MockedResponse:
            def json(self):
                return {
                    "wrong_id":123456789,
                    "kakao_account": {
                        "email" : "123456789@gmail.com"
                        }
                    }

        mocked_requests.get = MagicMock(return_value = MockedResponse())
        headers             = {"HTTP_Authorization": "3242343242343234"}
        response            = client.get("/users/login", headers)

        self.assertEqual(response.status_code, 400)
```


* * * 

## pytest 예시
- **외부 라이브러리인 pytest를 이용해서 unittest를 구현해보기**
- 굳이 pytest를 사용하는 이유는, python3에서 unittest보다 사용하기가 훨씬 직관적이기 때문. 또한, unittest 코드도 더 간결하게 만들기 쉽다.

```python
# 처음 설치는 
pip install pytest (pipenv install pytest)
```

- pytest는 파일 이름 앞부분에 test_ 라고 되어있는 파일들만 테스트 파일이라고 인식하고 실행한다. ex) test_example.py
  - 함수도 마찬가지이다. 함수 이름 앞부분에 test_ 라고 되어있는 함수들만 실제 unittest 함수로 인식하고 실행시킨다.
  - multiply_by_two 함수를 예로 들어보자. 해당 함수를 테스트하는 unit test 파일의 예시는 다음과 같다.

```python
# test_multiply_by_two.py 
def multiply_by_two(x):
    return x * 2

def test_multiply_by_two():
    assert multiply_by_two(4) == 7


# 파일 저장 후, pytest 실행하기
pytest
```

- **def test_multiply_by_two():** 
  - 이 부분이 multiply_by_two 함수를 테스트하는 unit test 함수이다. 함수 이름 앞 부분이 test_ 로 시작되어야 pytest가 unit test로 인식하고 실행한다.

- **assert multiply_by_two(4) == 7**
  - multiply_by_two(4)가 7을 리턴하는지 테스트한다. 8을 리턴하는 게 정상이므로 이 부분은 실행하게 되면 AssertionError가 발생할 것이다.

- 이렇게 구성했다면, 마지막으로 터미널에 pytest을 입력해서 unit test를 실행시켜 문제가 없는지 확인할 수 있다.


* * *

## Github Action이란
- Github Action이란 Github 저장소를 기반으로 소프트웨어 개발 Workflow를 자동화 할 수 있는 도구이다. 
- Github 내부에서 프로젝트를 빌드, 테스트, 릴리즈 또는 배포를 지원하는 기능으로서, Github에서 제공하는 CI/CD 도구라고 생각하면 된다.

### GitHub Action 주요 개념
- **Workflow**
  - **Workflow는 프로젝트를 빌드, 테스트, 패키지, 릴리스 또는 배포하기 위한 전체적인 프로세스이다.**
  - Workflow는 여러개의 Job으로 구성되어있고 event기반으로 동작한다.
  - Workflow는 최상위 개념이다.
  - 나만의 동작을 정의한 Workflow file을 만들어 전달하면 Github Action이 실행한다.
  - **Workflow 파일은 yml으로 작성되고, Github Repository의 .github/workflows 폴더 아래에 저장된다.**

- **Event**
  - **Workflow를 Trigger(실행)하는 특정 활동이나 규칙**
  - 예를 들어 다음과 같은 상황을 사용할 수 있다.
    - 특정 브랜치로 Push
    - 특정 브랜치로 Pull Request
    - 특정 시간대에 반복(Cron)
    - Webhook을 사용해 외부 이벤트를 통해 실행

- **Job**
  - **Job은 여러 Step으로 구성되고, 가상 환경의 인스턴스에서 실행된다.**
  - 다른 Job에 의존 관계를 가질 수 있고, 독립적으로 병렬 실행도 가능하다.

- **Step**
  - **Step은 순차적으로 명령어를 수행한다.**
  - Task들의 집합으로, 커맨드를 날리거나 action을 실행할 수 있다.

- **Action**
  - **Workflow의 가장 작은 블럭**
  - Job을 만들기 위해 Step들을 연결할 수 있다.
  - 재사용이 가능한 컴포넌트
  - 개인적으로 만든 Action을 사용할 수도 있고, Marketplace에 있는 공용 Action을 사용할 수도 있음(Github Marketplace와 Github Actions Repository에서 확인 가능)

- **Runner**
  - **Gitbub Action Runner 어플리케이션이 설치된 머신으로, Workflow가 실행될 인스턴스**
  - **Github에서 호스팅해주는 Github-hosted runner와 직접 호스팅하는 Self-hosted runner로 나뉨**
  - Github-hosted runner는 Azure의 Standard_DS2_v2로 vCPU 2, 메모리 7GB, 임시 스토리지 14GB


### CI Workflow 적용
- [해당 블로그 참고하기](https://insight-bgh.tistory.com/m/473)

- **Master 브랜치에 Push 또는 Pull Request가 올 경우 실행되는 CI라는 이름을 갖는 Workflow yml 파일 예시**
```yml
name: Java CI with Gradle

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'
        
    - name: Grant execute permission for gradlew
      run: chmod +x gradlew
      
    - name: Build with Gradle
      run: ./gradlew build
```

- **on 부분은 이벤트를 정의하는 부분이다.** 
  - 여기서는 master branch로 push 또는 pull request가 일어날 경우 해당 workflow가 실행된다.

- **jobs 부분을 살펴보자.**
  - 위의 파일은 build라는 job을 생성하고, 그 아래에 3개의 step이 존재하는 구조이다.
  - runs-on은 어떤 OS에서 실행될지 지정한다.
  - steps의 uses는 어떤 액션을 사용할지 지정한다. 이미 만들어진 액션을 사용할 때 지정한다.


### Python에 Github Action 적용 예시
- [관련 블로그](https://zzsza.github.io/development/2020/06/06/github-action/)
  - pytest 적용 내용도 포함


* * *

## Docker란
- [강의 내용 참고하기](https://url.kr/25buqm)
- 서버를 운영하다보면, 더 성능 좋은 서버로 옮겨가거나 늘어난 접속량을 처리하기 위해 서버를 여러 개 추가해야 될 수도 있다. 그럼 그 서버들에서도 똑같이 필요한 패키지나 라이브러리 등을 설치해줘야 하는데, 도커는 이 문제를 해결해준다. 또한, 같은 서버에서도 다양한 서비스를 운영하는 경우 각각이 다른 실행환경에서 동작해야할 때도 있다. 기존에는 JAVA 7으로 돌리고 있었는데, 새 서비스는 JAVA 8에서 동작하는 거라면 버전 문제를 신경써야 하지만 이것도 도커가 해결해줄 수 있다. 
  - **도커는 각 요소들이 설치된 모습을 "이미지"라는 형태로 박제해서 저장한다. 이 도커 이미지들은 "DockerHub"라는 곳에 업로드돼서 공유되고 다운받을 수 있게 된다. 그리고 이렇게 이미지로 저장된 항목들이 함께 연결돼서 동작하도록 설정된 상태를 명령어 텍스트나 문서 형태로 저장할 수 있다.**
  - **마치 이것들을 설치하는 과정을 어디서든 컴퓨터가 자동으로 재현할 수 있도록 녹화해두는 느낌이다. 그래서 이 문서만 잘 보관해주면 이 요소들을 언제 어디서든 미리 지정된 서비스에 필요한 설정대로 DockerHub으로부터 받아져서 설치를 할 수 있게 된다.**
  - **또한, 도커는 이 이미지의 요소들을 컴퓨터에 바로 설치하지 않는다. 각각을 "컨테이너"라고 불리는 독립된 가상 공간을 만들어내서 그 안에 설치를 하게 된다. 그래서 서로 다른 자바의 버전을 돌리는 서비스들도 각각 다른 컨테이너 안에서 서로 방해없이 돌아갈 수 있게 해준다.**

### Dockerfile
- **기본적으로 루트 디렉터리 내부에 Dockerfile를 생성해서 docker가 실행할 명령어들을 입력해놓고, 한 컨테이너를 만들어 Mysql같은 db나 redis, uwsgi를 구성해서 이미지를 생성해서 EC2에 빌드할 수 있게 해주는 것이 바로 Docker이다.**
  - 이렇게 Docker를 이용하면, 컨테이너를 병렬적으로 관리해서 compose로 개발을 하게되면 패키지, 라이브러리 디펜던시, DB 등을 동일한 환경에서 개발할 수 있게 된다.
  - Docker 컨테이너 안에 있는 DB나 Redis는 django를 대입해보면, settings.py에서 모두 도커를 바라보게끔 설정해야 한다...?
  - **즉, 모든 요소들이 도커 이미지를 바라보기 때문에 의존성이 주입된다.**

### docker-compose.yml 활용하기
- **dockerfile은 하나의 컨테이너를 만들어 환경을 구축한다고 하면, docker-compose는 여러개의 컨테이너를 묶어서 서비스를 구축한다고 생각하면 된다.**
  - 만약, Django + Nginx + Gunicorn을 한꺼번에 사용하는 서비스를 만든다고 한다면, docker를 사용하지 않는다면 하나의 가상 환경을 구축하여 저 패키지들을 각각 설치해서 쓸테고, docker를 쓴다 하더라도 하나의 컨테이너에 모두 모아서 할수도 있을 것이다.
  - **그러나, 도커에서는 이러한 방법보다는 여러개의 컨테이너로 프로세스들을 분산하여 이를 묶어서 쓰는것을 권장하고 있고, Docker-compose를 이용하면 이 작업을 훨씬 간편하게 처리할 수 있다.**

```python
version: '3.4'

services:
  backend:
    build: .
    container_name: backend
    entrypoint: sh -c "pipenv run python manage.py collectstatic --no-input && pipenv run uwsgi --ini uwsgi.ini"
    ports:
      - 8000:8000
    volumes:
      - ./:/app/

    depends_on:
      - mysql
      - redis

    restart: always

  mysql:
    image: 'bitnami/mysql:5.7.38-debian-11-r9'
    container_name: mysql
    ports:
      - 3306:3306
    volumes:
      - ../mysql:/bitnami/mysql/data
    environment:
      #- ALLOW_EMPTY_PASSWORD=yes
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=game_duo
      - MYSQL_USER=teamH
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}

    restart: always

  nginx:
    image: nginx
    container_name: nginx
    ports:
      - 80:80
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
      - ./static/:/static/

    depends_on:
      - backend

    restart: always

  redis:
    image: redis:7.0
    container_name: redis
    command: redis-server --port 6379
    ports:
      - 6379:6379
    restart: always
```

- 위의 코드들은 docker-compose.yml의 예시이다. services: 하위에 순서대로 backend, mysql, nginx, redis 컨테이너가 구분되어있다.
- [관련 블로그](https://my-repo.tistory.com/31)
- [관련 블로그2](https://leffept.tistory.com/330?category=950490)
- [docker-compose 설정](https://www.daleseo.com/docker-compose-file/)
- [Docker 설치](https://www.daleseo.com/docker/)
- [Docker compose 파일 내용](https://www.44bits.io/ko/post/almost-perfect-development-environment-with-docker-and-docker-compose#volumes-1)
- [EC2에 Docker, Docker compose 설치하기](https://megazonedsg.github.io/1-Make-Docker/#1-docker-compose-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)

* * *

## Docker 및 Docker-compose 예시
- [Docker 및 Docker-compose 예시](https://github.com/tkdqor/TIL/blob/main/%EB%B0%B0%ED%8F%AC/Docker%20%EB%B0%8F%20Docker-compose%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0.md)

* * *

## Docker 이미지와 컨테이너 차이
- **Docker 이미지**란, 특정 프로세스를 실행하기 위한(즉, 컨테이너 생성(실행)에 필요한) 모든 파일과 설정값(환경)을 지닌 것으로 더 이상의 의존성 파일을 컴파일하거나 이것저것 설치할 필요 없는 상태의 파일을 의미
  - ex) Ubuntu이미지는 Ubuntu를 실행하기 위한 모든 파일을 가지고 있으며, Oracle 이미지는 Oracle을 실행하는데 필요한 파일과 실행명령어, port 정보 등을 모두 가지고 있음을 의미
  - 그래서 이미지는 상태 값을 가지지 않고 변하지 않는다.
  - 하나의 이미지는 여러 컨테이너를 생성할 수 있고, 컨테이너가 삭제되더라도 이미지는 변하지 않고 그대로 남아 있게 된다.
  - 도커 이미지들은 github과 유사한 서비스인 DockerHub를 통해 버전 관리 및 배포(push&pull)가 가능하다.
  - 도커는 Dockerfile이라는 파일로 이미지를 만든다. Dockerfile에는 소스와 함께 의존성 패키지 등 사용했던 설정 파일을 버전 관리하기 쉽도록 명시한다.

- **Docker 컨테이너**란, 이미지(Image)를 실행한 상태에서 응용프로그램의 종속성과 함께 응용프로그램 자체를 패키징 or 캡슐화하여 격리된 공간에서 프로세스를 동작시키는 기술을 의미
  - 컨테이너를 삭제했다는 것은 컨테이너에서 생성한 파일이 사라진다는 의미
  - 한 서버는 여러 개의 컨테이너를 가져도 당연히 상관없으며, 컨테이너는 각각 독립적으로 실행된다.
  - 컨테이너는 커널 공간과 호스트OS 자원(시스템 콜)을 공유한다.
  - 컨테이너는 종료되었다고 해도 메모리에서 삭제되지않고 남아있다. 삭제하려면 명시적으로 삭제해야한다.

- [참고 블로그](https://velog.io/@ragnarok_code/%EB%8F%84%EC%BB%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Container%EC%99%80-%EC%9D%B4%EB%AF%B8%EC%A7%80Image%EB%9E%80)

* * *

## 컨테이너와 가상머신의 차이
- 컨테이너와 가상머신 모두 다양한 IT 요소들을 결합해서 시스템의 나머지 부분으로부터 분리하는 패키지 컴퓨팅 환경이라는 점에서 유사하다고 볼 수 있다. **차이점이라면 확장방식과 이식성이다.**

- **차이점**
  - **컨테이너**는 일반적으로 크기가 MB단위이며, 특정 작업을 수행하는 단일 기능(마이크로서비스)이 컨테이너에 패키징되는 경우가 많다. 그리고 컨테이너는 완전한 가상 머신보다 리소스를 더 적게 사용하고 호스트 메모리에 가해지는 부담을 줄일 수 있도록 운영 체제 환경(커널)을 서로 공유한다.
  - **가상머신**은 일반적으로 크기가 GB단위이며, 자체 OS를 포함하고 있다. 즉, 각 가상 머신마다 전용 운영 체제가 있기 때문에 가상 머신에 구축된 애플리케이션을 실행할 때 메모리 사용량이 필요 이상으로 많아져 가상 머신이 호스트에 필요한 리소스를 모두 사용할 수 있다.

- [관련 블로그](https://born-dev.tistory.com/39)

