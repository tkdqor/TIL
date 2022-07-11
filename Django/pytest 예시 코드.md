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

* * *

- **이번에는 좀 더 복잡한 POST 요청인 /tweet 엔드포인트를 테스트해보자.**
- /tweet 엔드포인트를 통해 tweet를 생성하기 위해서는 먼저 사용자가 있어야 한다. 또한, 해당 사용자로 인증 절차를 거친 후 access token를 받은 후, tweet 엔드포인트를 호출해야 한다. 

```python
def test_tweet(api):
    ## 테스트 사용자 생성                                        1번
    new_user = {
               ‘email’ : ‘aumsbk@naver.com’,
               ‘password’ : ‘test_password’,
               ‘name’ : ‘상백',
               ‘profile’ : ‘test profile’
    }

    resp = api.post(
               ‘/sign-up’,
               data = json.dumps(new_user),
               content_type = ‘application/json’
    )
    assert resp.status_code == 200


    ## new user의 id를 가져오기                                              2번
    resp_json = json.loads(resp.data.decode(‘utf-8’))
    new_user_id = resp_json[‘id’]
    
    ## 로그인 하기                                                                     3번
    resp = api.post(
            ‘/login’,
            data = json.dumps({‘email’ : ‘aumsbk@naver.com’,
            ‘password’ : ‘test_password’}),
            content_type = ‘application/json’
    )
    resp_json = json.loads(resp.data.decode(‘utf-8’))
    access_token = resp_json[‘access_token’]


    ## /tweet를 POST로 날리기                                                 4번
    resp = api.post(
            ‘/tweet’,
            data = json.dumps({‘tweet’ : “Hello World!”}),
            content_type = ‘application/json’,
            headers = {‘Authorization’ : access_token}
    )
    assert resp.status_code == 200


    ## /timeline/{new_user_id}를 GET으로 날려서 생성한 트윗 내역 확인하기     5번
    resp = api.get(f’/timeline/{new_user_id}’)
    tweets = json.loads(resp.data.decode(‘utf-8’))

    assert resp.status_code == 200
    assert tweets == {
            ‘user_id’ : 1,
            ‘timeline’ : [
                  {
                         ‘user_id’ : 1,
                         ‘tweet’ : “Hello World!”
                  }
            ]
    }
```

- 1번 ⇒ tweet를 보낼 테스트 사용자를 먼저 생성한다. /sign-up 엔드포인트를 호출하고 생성하며 호출 후 응답의 status code가 OK 200인지 확인
- 2번 ⇒ 1에서 새로 생성한 사용자의 아이디를 1에서 받은 JSON 응답에서 읽어 들인다. 밑에 /timeline 엔드포인트를 호출할 때 사용자 아이디가 필요하다.
- 3번 ⇒ /tweet 엔드포인트에 HTTP 요청을 전송하려면 access token이 필요하므로 /login 엔드포인트를 먼저 호출해서 access token를 읽어들인다.
- 4번 ⇒ /tweet 엔드포인트를 호출해 tweet를 생성한다. 3에서 생성한 access token를 “Authorization” 헤더에 첨부해서 request를 보낸다.
- 5번 ⇒ 4에서 생성한 tweet이 정상적으로 생성되었는지를 확인하기 위해 timeline 엔드포인트를 호출해서 응답을 확인한다. timeline 엔드포인트를 호출할 때 2에서 읽어들인 사용자 아이디를 사용한다.

* * *

- **또 한 가지 반복적으로 필요한 것은 각 테스트에서 생성된 데이터를 지우는 일이다.**
  - 각 테스트는 독립적이어야 하며 서로 영향을 주면 안 된다. 
  - ex) tweet 엔드포인트 테스트 코드에서 생성된 사용자를 지우지 않으면 그 다음에 동일한 사용자를 생성할 때 이메일 주소가 같기 때문에 오류가 날 것이다. 그러므로 각각의 테스트마다 생성했던 데이터를 각 테스트가 종료된 후 삭제해 주어야 다른 테스트에 영향을 끼치지 않는다.


- **다행히도 pytest에서는 각 테스트 실행 바로 전 / 그리고 실행 바로 후에 실행할 코드를 지정해 놓아 자동으로 적용시킬 수 있게 해준다. setup_function과 teardown_function을 사용하면 된다. setup_function은 각 테스트가 실행되기 전에 실행이 되고, teardown_function은 각 테스트가 종료된 후 실행이 된다.**
  - 그러므로, 사용자를 생성하는 코드는 setup_function 함수에서 구현하고, test 데이터를 삭제하는 코드는 teardown_function에서 구현하도록 하자.

```python
import bcrypt

def setup_function():                                            1번
      ## 테스트 유저 생성하기
      hashed_password = bcrypt.hashpw(
            b”test password”,
            bcrypt.gensalt()
      )
      new_user = {                                                     2번
               ‘id’ : 1,
               ‘name’ : ‘김상백',
               ‘email’ : ‘aumsbk@naver.com’,
               ‘profile’ : ‘test profile’,
               ‘hashed_password’ : hashed_password
      }
      database.execute(text(“””
               INSERT INTO users(
                        id,
                        name,
                        email,
                        profile,
                        hashed_password
               ) VALUES (
                        :id, 
                        :name,
                        :email,
                        :profile,
                        :hashed_password
               ) 
          “””), new_user)

def teardown_function():                                          3번
      database.execute(text(“SET FOREIGN_KEY_CHECKS=0”))        4번
      database.execute(text(“TRUNCATE users”))
      database.execute(text(“TRUNCATE tweets”))	
      database.execute(text(“TRUNCATE users_follow_list”))
      database.execute(text(“SET FOREIGN_KEY_CHECKS=1”))        5번

def test_tweet(api):
    ## 로그인
    resp = api.post(
            ‘/login’,
            data = json.dumps({‘email’ : ‘aumsbk@naver.com’,
            ‘password’ : ‘test_password’}),
            content_type = ‘application/json’
    )
    resp_json = json.loads(resp.data.decode(‘utf-8’))
    access_token = resp_json[‘access_token’]
    ## tweet
    resp = api.post(
            ‘/tweet’,
            data = json.dumps({‘tweet’ : “Hello World!”}),
            content_type = ‘application/json’,
            headers = {‘Authorization’ : access_token}
    )
    assert resp.status_code == 200
    
    ## tweet 확인
    resp = api.get(f’/timeline/1’)
    tweets = json.loads(resp.data.decode(‘utf-8’))

    assert resp.status_code == 200
    assert tweets == {
            ‘user_id’ : 1,
            ‘timeline’ : [
                  {
                         ‘user_id’ : 1,
                         ‘tweet’ : “Hello World!”
                  }
            ]
    }
```

- 1번 ⇒ 또 setup_function을 통하여 각 test 함수가 실행되기 전에 필요한 사용자를 생성하도록 한다. 데이터베이스에 직접 생성한다.
- 2번 ⇒ 여기서 중요한 건 사용자 아이디를 자동으로 생성하게 하지 말고, 고정 값을 정해주는 것이다. 그래야 실제 unit test 함수에서 해당 사용자 아이디를 알 수 있다.
- 3번 ⇒ teardown_function를 통해 test 데이터를 전부 삭제해준다. TRUNCATE SQL 구문은 해당 테이블의 데이터를 모두 삭제해준다.
- 4번 ⇒ TRUNCATE SQL 구문을 실행할 때 해당 테이블에 외부 키가 걸려 있으면 테이블의 데이터들을 삭제할 수 없다. 그래서 임의로 SET_FOREIGN_KEY_CHECKS=0 SQL 구문을 실행하여 외부 키를 잠시 비활성화시킨다. 실제 서비스 중인 데이터베이스에서는 실행하면 안된다!
- 5번 ⇒ 4에서 비활성화시켰던 외부 키를 다시 활성화시킨다.






