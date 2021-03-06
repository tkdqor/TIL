## django-debug-toolbar의 필요성
- 대개의 데이터베이스 기반 애플리케이션에서는 데이터베이스 I/O(Inupts/Ouputs)가 병목이다. 동일한 작업을 수행하더라도, 보다 적은 수의 SQL쿼리를 수행하고, 보다 최적화된 쿼리를 사용해야한다.
- django-debug-toolbar를 통해 장고를 통한 애플리케이션 개발에서 어떤 SQL이 수행되고 있는 지 확인 및 다른 내역 등을 확인해볼 수 있다.


## django-debug-toolbar
- 현재 Request / Response에 대한 다양한 디버깅 정보를 알려준다. 다만 응답 포맷이 HTML 응답일 때만 다양한 정보를 보여주는 것이 가능하다. API 응답에서는 보여줄 수 없다.
- ajax 비동기 자바스크립트를 통한 API 요청에서는 실제 응답에 포함되어 있어도 웹 브라우저에서 볼 수 없기 때문에 이에 대한 지원은 불가능하다.
- django-debug-toolbar는 다양한 panel를 지원한다.
  - 종류가 많은데, 그 중에 SQL Panel를 통해 현재 요청을 처리하는 데에 발생한 SQL 내역을 확인할 수 있다. 


### django-debug-toolbar 설치
- 공식문서 https://django-debug-toolbar.readthedocs.io/en/latest/installation.html 
- debug-toolbar에서 처리해줘야 할 url들이 있다. 그래서 프로젝트 디렉터리 내부에 있는 urls.py에서 설정해줘야 한다.
- **middleware란, MVT에서 View가 호출되기 전에 혹은 호출되고 나서 호출이 되는 여러 함수들이 있다. 그런 함수들을 middleware라고 한다.** 이 middleware 단에다가 debug-toolbar를 추가해줘야 한다. **이 middleware 단에서
  해당 응답에다가 디버깅 정보들을 추가해주는 것이다.**
  - 마지막으로, settings.py에서 INTERNAL_IPS 라는 항목에 "127.0.0.1"를 넣어줘야 한다. django-debug-toolbar에서 출력되는 내용들이 아주 중요한 내용들이다. 보안과 관련된 민감한 내용들이 많기 때문에,
    아무리 개발 서버라고 할지라도 누구라도 접속했을 때 디버그 툴바를 띄울 수 있는 건 문제가 될 수 있다. 그래서 이 디버그 툴바를 허용하는 IP를 지정해주는 것이다. 127.0.0.1은 localhost, server를 띄우는 그 자신 server가 되기 때문에 그 컴퓨터에서만 디버그 툴바를 띄울 수 있는 것이다.
  - 혹은, 디버그 툴바를 띄울지 말지를 결정하는 로직을 만들고자 한다면 SHOW_TOOLBAR_CALLBACK 이라는 옵션을 구현하면 된다.

- 주의사항: 웹페이지의 template에 body element가 있어야만 디버그 툴바가 작동한다. 그 이유는 디버그 툴바의 html/script 디폴트 주입 타겟이 </body> 이기 때문이다.
  - 웹 페이지에서 우클릭하고 **페이지 소스 보기**를 클릭하면, server 응답에 debug-toolbar와 관련한 부분이 html과 css로 이루어져서 주입이 되고 있는 것을 확인할 수 있다. 이렇게 주입이 되려면, 주입할 타겟팅이 있어야 한다. 그 타겟팅이 바로 body 태그인 것이다.
  - settings의 INSERT_BEFORE 설정이 있는데 디폴트가 body end tag로 되어 있다.


### 설치 실습
```terminal
pip install django-debug-toolbar
```

- Successfully installed django-debug-toolbar-3.2.4 이렇게 설치를 완료하고, settings.py에 가서 INSTALLED_APPS에 'django.contrib.staticfiles' 라는 앱이 설치가 되어 있는지 확인. 그리고 'debug_toolbar', 이라고 추가해주기.

- 이제 프로젝트의 urls.py에다가 debug_toolbar 로직을 넣어주자. 이것도 debug일 때 넣어주면 된다.
```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog1/', include('blog1.urls')),   # 새롭게 url를 만들어주고 해당 url은 blog1 앱의 urls.py로 보내주기
    path('instagram/', include('instagram.urls')),  # 새롭게 url를 만들어주고 해당 url은 instagram 앱의 urls.py로 보내주기
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

    # debug_toolbar도 마찬가지로 DEBUG일 때만 적용
    import debug_toolbar
    urlpatterns += [
        path('__debug__/', include(debug_toolbar.urls)),
    ]
```

- path 함수를 사용해서 __debug__로 시작되는 주소는, debug_toolbar의 urls에서 처리하게끔 설정하자.

- 그리고 settings.py에 보면, **MIDDLEWARE**라는 목록이 있다. 여기에 있는 경로들을 실제로 다 가서 확인해보자. 대부분 function의 형태이다. 이 목록에 **'debug_toolbar.middleware.DebugToolbarMiddleware',** 이 코드를 추가한다.

- 마지막으로 **INTERNAL_IPS** 라는 항목을 settings.py에 직접 새로 입력해주면 된다.
```python
INTERNAL_IPS = ['127.0.0.1']
```

- 이렇게 설정하고 서버를 작동하여 웹페이지를 보면 

<img src="https://user-images.githubusercontent.com/95380638/152905592-e0739d07-e0c0-4161-8e3a-fa3acf6a3a38.png" width="40%" height="10%">

- 이런식으로 화면 우측에 디버그 툴바가 뜨게 된다. admin 페이지로 들어가도 마찬가지로 뜨게 된다.
  - 클릭할 수 있는 항목을 살펴보면, versions / Time / Settings / Headers / Request / SQL(각 sql문에 대한 수행시간 등을 확인 가능)
  - 이러한 정보들을 바탕으로 디버깅하기에 굉장히 좋다. 
  - instagram/ url로 입력했을 때를 보면, SQL에서는 우리가 직접 하지는 않았지만 session에서 가져오는 것도 동작을 하고, 현재 로그인된 유저의 정보를 가져오는 것도 동작한다. 그리고 instagram 앱의 Post 모델 목록을 id 역순으로 가져오고 있다.(해당 부분은 우리가 Post 모델의 Meta 클래스에다가 디폴트 정렬을 적용한 부분이 된다.)

- **나중에 개발을 하다가 처음보다 페이지가 느려진다는 기분이 든다면, SQL를 수시로 확인하면서 의도치 않게 SQL이 수백 개씩 실행되는 경우가 있다.** 그럴 때는 **queryset에서 prefetch_related와 select_related**라는 join query가 생기도록 queryset에 지정할 수 있다. **그래서 이 SQL 패널을 수시로 확인해보자.** 
- https://velog.io/@rosewwross/Django-selectrelated-%EC%99%80-prefetchedrelated%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%B0%B8%EC%A1%B0

* * *

## 코드를 통한 SQL 내역 확인
- QuerySet의 query 속성 참조 
  - ex) print(Post.objects.all().query) 이런식으로 queryset 에다가 .query 이렇게 query 속성을 사용해서 출력해보면 -> 실제 해당 queryset에 대한 SQL문, 쿼리를 확인할 수 있다.


- connection를 통해서 connection.queries를 하게 되면
```python
from django.db import connection, connections

for row_dict in connection.queries:
    print('{time} {sql}'.format(**row_dict))
    
connections['default'].queries
```

- 원래 django는 settings.DEBUG = TRUE 시에만 쿼리 실행내역을 메모리에 계속 누적하게 된다. 그러다가 server가 재시작할 때 clear가 된다. 지금 우리가 개발 서버를 구동하고 있는 상태에서, 만약 python 파일을 enter 치고 하나 저장했다면 -> 바로 server가 재시작하게 되는 것을 확인할 수 있다. 이렇게 자동 재시작이 되었으면 메모리의 누적이 clear가 되는 것이다. 
  - 메모리가 누적되더라도, 개발 서버에서는 python 파일이 변경되면 서버가 자동 재시작을 하기 때문에 메모리가 부족할 일이 없다. (단, html 파일에는 반응하지 않는다.)
  - 만약 나중에 실 서비스 production 상에서 DEBUG 옵션을 TRUE로 키게 되면, 실 서버에서는 우리가 이 개발 서버, 즉 runserver를 사용하지 않는다. 안쓰면 자동 재시작을 하지 않게 된다. 그래서 계속 켜져있고 계속 돌아가면, 메모리가 부족한 상황이 발생할 수 있다. 그러면 서비스가 되지 않는 것이다.

- 어떤 개발자는 django로 서비스를 하는데 DEBUG 옵션을 TRUE로 켜놓고 서비스를 하는 분들도 있다. 알파, 베타 테스트인 것으로 보이나 위험할 수 있으니 실 개발 상황에서는 꼭 꺼주는 게 좋다. 이러한 내용은 django 기본에서 지원해주는 부분이다. 


### 쿼리 초기화
- 메모리가 누적되기에, 직접 프로세스가 재시작 될 때 쿼리 초기화가 진행된다. 혹은 django.db.reset_queries()를 통해서 수동 초기화도 가능하다.

- 혹은, django-querycount 라는 이름의 라이브러리를 설치하면 SQL 실행내역을 개발 서버 콘솔에 표준 출력을 해준다. Ajax 요청 내역도 출력 가능하다.
