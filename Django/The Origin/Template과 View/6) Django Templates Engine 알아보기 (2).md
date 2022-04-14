## Django Templates Engine 알아보기 (2)
- liongram 프로젝트로 작업환경 세팅하기


### 프로젝트 단위로 templates 관리하기
- 먼저 프로젝트 디렉터리 내부 settings.py로 가면 TEMPLATES라는 변수가 있다. 여기에 DIRS에다가 template의 경로를 넣어주면 된다.
- settings.py 위쪽에 보면 BASE_DIR라는 변수가 있는데 이게 루트 디렉터리 위치를 의미한다. 그래서,

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

- **이렇게 'DIRS': [BASE_DIR / 'templates'] 이런식으로 넣어준다. 즉, 워크스페이스(루트 디렉터리 위치)의 templates 위치를 의미한다. 그래서 liongram 안에서 새폴더를 만들고 templates라고
  이름을 지어주자.**
  - **그리고나서 templates 하위에다가 앱명인 posts로 디렉터리를 만들어주자. 그 다음에 posts 하위에는 post_list.html를 만든다.**
  - 그 다음 차례대로 post_detail.html, post_form.html, post_confirm_delete.html까지 복사해서 만들어준다.


### posts App 내부 Views.py 코드 추가하기
