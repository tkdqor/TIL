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

- 위와 같이 AUTH_PASSWORD_VALIDATORS라는 변수가 정의되어 있다. 사용자 속성과 관련된 부분, 최소길이, 섞어서 써야하는 패스워드 등이 있는데, 

- **[django 공식 Github 레포지토리](https://github.com/django/django) 가 있다. django가 오픈소스이기 때문에 모든 코드가 이렇게 공개되어 있다.**
  - **여기서 django - contrib - auth - common-passwords.txt.gz 이라고하는 파일이 있다. 해당 파일은 zip 파일이어서 다운을 받고 열어봐야 한다. 이걸 열어보면 텍스트 파일이 있는데, 그 안에는
    기본적으로 비밀번호를 못 만들게 하는 것들이라고 보면 된다.**
  - **그래서 우리가 서버를 실행시키고 우리 서비스에서 회원가입 시, 비밀번호를 해당 파일에 적혀 있는 것들 중 하나를 입력해서 해보면 --> 가입이 되지 않는 것을 확인할 수 있다. 범용적인 비밀번호 테이블이라고도
    표현한다. 즉, 너무 일반적인 비밀번호라 해당 비밀번호로는 가입이 안 된다는 것을 의미한다.**
    
3:30
