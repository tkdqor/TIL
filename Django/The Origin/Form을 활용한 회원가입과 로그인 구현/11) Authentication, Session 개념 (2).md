## Authentication, Session 개념 (2)
- django에서는 session를 어떻게 사용해서 로그인을 처리하는지 확인해보기.
- settings.py를 보면,

```python
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]
```

- 위와 같이 AUTH_PASSWORD_VALIDATORS라는 변수가 정의되어 있다. 사용자 속성과 관련된 부분, 최소길이, 섞어서 써야하는 패스워드 등 관련된 것들이 나와있다.

- **[django 공식 Github 레포지토리](https://github.com/django/django) 가 있다. django가 오픈소스이기 때문에 모든 코드가 이렇게 공개되어 있다.**
  - **여기서 django - contrib - auth - common-passwords.txt.gz 이라고하는 파일이 있다. 해당 파일은 zip 파일이어서 다운을 받고 열어봐야 한다. 이걸 열어보면 텍스트 파일이 있는데, 그 안에는
    기본적으로 비밀번호를 못 만들게 하는 것들이라고 보면 된다.**
  - **그래서 우리가 서버를 실행시키고 우리 서비스에서 회원가입 시, 비밀번호를 해당 파일에 적혀 있는 것들 중 하나를 입력해서 해보면 --> 가입이 되지 않는 것을 확인할 수 있다. 범용적인 비밀번호 테이블이라고도
    표현한다. 즉, 너무 일반적인 비밀번호라 해당 비밀번호로는 가입이 안 된다는 것을 의미한다.**
    
- 그리고, settings.py에 AUTH_PASSWORD_VALIDATORS에서 MinimumLengthValidator를 주석 처리 해보면, 최소 8자리 이상을 처리해주는 validation이라는 것을 알 수 있다.
  - 다른 개인 정보와 유사한 비밀번호는 사용할 수 없다는 validation은 UserAttributeSimilarityValidator이다. 그래서 우리가 이걸 수정해볼 수도 있는 것이다. 
  - 통상적으로 자주 사용되는 비밀번호는 사용할 수 없다는 validation은 CommonPasswordValidator과 관련이 있다. "통상적"이라는 의미는 위에서 확인했던 것과 같이 텍스트 파일에 정의되어 있다.
  - 숫자로만 이루어진 비밀번호는 사용할 수 없다는 validation은 NumericPasswordValidator과 관련이 있다.
  - **즉, settings.py에서 이러한 값들을 추가하는 것 만으로도 form에서도 자동으로 연동이 된다는 게 가장 큰 핵심이라고 볼 수 있다.**


### 로그인 진행 과정 - 세션과 쿠키
- **먼저 로그인 화면 페이지에서 개발자 도구를 키고, 어플리케이션에 들어가면 다양한 메뉴가 보이는데, 여기서 Storage - Cookies를 클릭하면 --> 우리의 url인 http://localhost:8000이 뜨게 된다.** 그걸 누르면 csrftoken 이라는 게 나오고, 이 상태에서 로그인을 하게 되면 몇가지가 생긴다.
  - 언더바ga 라는 것도 생기고, sessionid 라는 것도 생긴다. 우리가 로그인할 때 session를 사용한다고 했다. 
  - **로그인을 하고나서 다시 로그인을 하면서 서버한테 요청을 날릴 때는 아이디와 비밀번호를 다시 같이 날려줘야 하고, 그래야 서버에서 이 유저는 이전에 로그인을 했던 유저라고 판단을 할 수 있게 된다. 그 때 사용하는 게 이 sessionid라는 값이다. 그래서 우리가 로그인할 때 서버는 그걸 처리하면서 사용자인 클라이언트 쿠키에다가 세션 id값을 이렇게 넣어주게 된다.(지금 우리가 쿠키를 보고 있으니까 확인할 수 있는 것)**
  - **그리고 이 사용자가 게시글을 작성하기 위해 다른 페이지를 요청하면, 세션 id값을 서버가 스스로 확인을 해서 이 유저가 어떤 유저인지 판단을 할 수 있다.**
  - 우리가 로그인 기능을 구현할 때, login이라는 함수를 사용했는데, 이 함수를 command로 들어가보면 session과 관련된 코드들이 존재한다. 이 코드가 존재하는 건, 클라이언트 쿠키에 세션 id값을 넣어주기 위해서인 것이다.

- **반대로, 로그아웃을 하게 되면 logout이라는 함수를 사용하고 이 함수도 command로 들어가보면, session에 대한 값을 날려버리게 된다. 실제로 로그아웃을 눌러보면 sessionid가 사라지는 것을 볼 수 있다.** 우리가 로그인 / 로그아웃 기능을 구현할 때는, 우리가 직접 세션을 건들지 않고 django에서 이미 기능 구현을 해놓은 것을 가지고서 사용할 수 있는 것이다. 

- **다시 로그인을 할 때, 세션 id값이 생성되고 b68f1keqeevdofpx7v4749ozipgwg8fi 이렇게 우리가 해석할 수 없는 값이 생성된다. 그럼 이 값을 서버에서는 어딘가에 저장을 해두고서 어떤 유저라는 걸 판단하는데, django에서는 기본적으로 데이터베이스에 그 session 값들을 저장해놓고 있게 된다.**
- DBeaver에서 확인해보면, 우리가 따로 모델을 만들지 않았어도 **django_session이라는 테이블**이 자동으로 생기게 된다. 해당 모델을 들어가보면, session_key라는 필드가 있고 우리의 세션 id값이 동일하게 들어가 있다. 즉, 클라이언트한테 넘겨준 key값이 데이터베이스에도 그대로 저장되고 있는 것이다. 그리고 session_data라는 필드도 있는데 이걸 서버가 해석해서 이 유저는 어떤 유저구나 라고 판단을 하게 된다. 그 다음, 우리가 request.user라는 것을 사용할 수 있게 되는 것이다. 
  - **그래서 middleware.py에서 AuthenticationMiddleware 클래스를 확인해보면, request가 왔을 때 request에 session이 있는지 확인하고 session이 있다면 --> request.user = SimpleLazyObject(lambda: get_user(request)) 이렇게 get_user로 request를 넣어서 request.user를 사용할 수 있게 해준다.**
  - get_user를 command로 가고, auth의 get_user를 호출하니까 이것도 command로 가보면, def get_user에서 user의 session key를 가지고 오게 된다. 그리고 이걸 가지고 user를 응답해주는 코드인 것이다. 

- **그래서 로그인 요청이 왔을 때, session 키값을 가지고서 데이터베이스 조회를 해서 특정 유저가 누구인지를 판단을 한 다음 로그인을 시켜주는 것이다.**

- **우리가 로그인이 된 상태에서, debug toolbar의 SQL를 클릭해보면 --> 우리가 날리지도 않은 쿼리문들이 찍혀있다.**

```terminal
SELECT ••• FROM "django_session" WHERE ("django_session"."expire_date" > '''2022-05-04 14:18:08.675306''' AND "django_session"."session_key" = '''b68f1keqeevdofpx7v4749ozipgwg8fi''') LIMIT 21

SELECT ••• FROM "users_user" WHERE "users_user"."id" = '1' LIMIT 21
 2 similar queries.  Duplicated 2 times.		
```

- **이렇게 django_session 모델 테이블에 조회를 요청하고 그리고 and조건으로 session_key값도 우리와 동일한 값으로 해당 데이터를 요청하고 있다. 그리고 그 밑에 쿼리까지 보면, 지금 로그인한 유저가 어떤 유저인지 django의 User 모델 테이블에 요청하고 응답을 해주게 되는 것이다. 그래서 이 2개의 쿼리를 로그인할 때 기본으로 요청되는 쿼리라고 보면 된다.**
  - 즉, 사용자가 요청을 날렸을 때 세션에 키값이 있다고 하면 이 키값을 갖고 데이터베이스를 조회하는 쿼리가 무조건 날라간다는 것이다. 그래야지 서버에서는 이 사용자가 인증된 사용자인지 판단을 할 수 있다는 것이다. 


- **쿠키는 서버에서 클라이언트한테 쿠키 부스러기를 던져놓고, 그걸 하나씩 꺼내먹는다는 느낌이다. 즉, 서버가 클라이언트한테 뭔가를 남겨놓기 위해 넣는 공간을 쿠키라고 한다.**
  - 쿠키는 "쇼핑몰에서 비회원인데 장바구니를 담을 수 있는 경우"가 있는데, 그런 상황에서는 쿠키를 사용하게 된다. 서버에는 데이터가 없으니 클라이언트에 있는 쿠키 값만 넣어주는 것이다. 

- **우리가 지금은 session id값을 데이터베이스에 저장을 해놓고 있지만, 이걸 캐싱 서버라든지 파일이라든지 뭔가 다양한 형태로 서버에서 알 수 있도록 기록해놓고 이걸 쿠키에 넣어서 활용한다는 이 과정이 --> 세션의 방식이라고 보면 된다.** 이러한 session은 인증에서도 많이 사용한다. 
  - 만약 데이터베이스에 있는 세션 값을 지우게 되면 어떨까? 그러면 로그인이 바로 풀리게 된다. 
  - 그래서 만약에 우리가 세션 기반으로 인증 시스템을 사용하고 있다면, 세션 시스템이 초기화되거나 데이터가 날라가게 되면 자동으로 로그아웃이 된다는 것이다. 





