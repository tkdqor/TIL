## pytest 예시 코드
```python
from app import create_app

@pytest.fixture
def api():
      app = create_app(config.test_config)
      app.config[‘TEST’] = True
      api = app.test_client()
      
      return api
```

⇒ app.py파일에서 create_app 함수 임포트     
⇒ @pytest.fixture → pytest의 fixture decorator를 적용. 이 데코레이터를 적용하는 것이 중요! fixture decorator가 적용된 함수와 같은 이름의 인자(parameter)가 다른 test 함수에 지정되어 있으면 pytest가 알아서 같은 이름의 함수의 리턴 값을 해당 인자에 넣어준다.           
⇒ def api(): → 이건 fixture 함수 이름 / 함수 이름이 중요! / 동일한 이름의 인자를 해당 함수의 리턴값으로 적용시켜 주기때문.           
⇒ app = create_app(config.test_config) → 임포트한 create_app 함수를 사용해서 우리가 테스트할 미니터 api 애플리케이션 생성 / 설정을 test_config을 create_app 함수에 인자로 넘겨줌으로써 데이터베이스 설정이 모두 테스트 설정이 되도록 함.           
⇒ app.config[‘TEST’] = True → TEST 옵션을 True로 설정해줌으로써 Flask가 에러가 났을 경우, HTTP 요청 오류 부분은 핸들링하지 않음으로써 불필요한 오류 메세지는 출력하지 않게 한다.           
⇒  api = app.test_client() → test_client() 함수를 호출해서 테스트용 클라이언트를 생성 / 그래서 URL 기반으로 원하는 엔드포인트들을 호출할 수 있게 된다.      
⇒ return api → 위에서 호출한 test client를 리턴해준다


```python
def test_ping(api):
      resp = api.get(‘/ping’)
      assert b’pong’ in resp.data
```

⇒ 이제 생성한 test client를 사용해서 /ping 엔드포인트를 테스트하는 코드를 구현해보자.          
⇒ 위에 테스트 함수가, test client를 통해서 /ping URL에 GET 요청을 보내는 함수이다.          
⇒ def test_ping(api): → ping 엔드포인트를 테스트하는 함수이다. 함수 앞부분이 test_로 시작해서 test 함수로 인식된다. 또한, 중요한 것이 api 인자가 들어간 것이다. pytest 커맨드를 실행해서 테스트들을 실행하므로 test 함수들에 인자를 넘겨주어 실행할 수 없다. 그런데도 인자가 지정되어있는 이유는, pytest가 자동으로 지정된 인자와 동일한 이름을 가지고 있고, 또한 pytest fixture decorator가 적용되어있는 함수를 찾아서 해당 함수의 리턴 값을 인자에 적용시켜 주기 때문이다. 그러므로, 여기서는 바로 앞서 구현한 api 함수의 리턴값, 즉 test client가 자동으로 적용된다.        
⇒ resp = api.get(‘/ping’) → 이제 api가 test client이니까,, test client의 get 메서드를 통해서 가상의 GET 요청을 /ping URI와 연결되어있는 엔드포인트에 보낸다.        
⇒ assert b’pong’ in resp.data → 위의 코드를 실행한 다음, 리턴받은 응답(resp)의 body에 “pong”이라는 텍스트가 포함되어 있는지 확인한다. 포함되어 있으면 정상적으로 작동한 것이므로 테스트가 성공한 것이며, 그렇지 않다면 실패한 것으로 간주된다. 여기서 “pong”이라는 스트링 앞에 붙은 b는 해당 스트링을 byte로 변환시켜 주는 역할을 한다. resp.data가 스트링이 아니라 byte이므로 변환해서 비교하는 것이다. 

```python
pytest -p no:warnings -vv
```

- 다음과 같이 터미널에 입력해서 테스트를 진행한다. 
- /ping 엔드포인트가 가장 간단한 엔드포인트이긴 하지만, 다른 엔드포인트들을 테스트 하는 것도 방식은 동일하다. test client를 통해 가상의 HTTP 요청을 전송해 해당 엔드포인트를 호출한 후 결과값을 테스트하는 것이다. 



