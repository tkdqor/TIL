## django-debug-toolbar
- 현재 Request / Response에 대한 다양한 디버깅 정보를 알려준다. 다만 응답 포맷이 HTML 응답일 때만 다양한 정보를 보여주는 것이 가능하다. API 응답에서는 보여줄 수 없다.
- ajax 비동기 자바스크립트를 통한 API 요청에서는 실제 응답에 포함되어 있어도 웹 브라우저에서 볼 수 없기 때문에 이에 대한 지원은 불가능하다.
- django-debug-toolbar는 다양한 panel를 지원한다.
  - 종류가 많은데, 그 중에 SQL Panel를 통해 현재 요청을 처리하는 데에 발생한 SQL 내역을 확인할 수 있다. 


### django-debug-toolbar 설치
- 공식문서 https://django-debug-toolbar.readthedocs.io/en/latest/installation.html 
- debug-toolbar에서 처리해줘야 할 url들이 있다. 그래서 프로젝트 디렉터리 내부에 있는 urls.py에서 설정해줘야 한다.
- middleware란, MVT에서 View가 호출되기 전에 혹은 호출되고 나서 호출이 되는 여러 함수들이 있다. 그런 함수들을 middleware라고 한다. 이 middleware 단에다가 debug-toolbar를 추가해줘야 한다. 이 middleware 단에서
  해당 응답에다가 디버깅 정보들을 추가해주는 것이다.
  - 마지막으로, settings.py에서 INTERNAL_IPS 라는 항목에 "127.0.0.1"를 넣어줘야 한다. django-debug-toolbar에서 출력되는 내용들이 아주 중요한 내용들이다. 보안과 관련된 민감한 내용들이 많기 때문에,
    아무리 개발 서버라고 할지라도 누구라도 접속했을 때 디버그 툴바를 띄울 수 있는 건 문제가 될 수 있다. 그래서 이 디버그 툴바를 허용하는 IP를 지정해주는 것이다. 127.0.0.1은 localhost, server를 띄우는 그 자신 server가 되기 때문에 그 컴퓨터에서만 디버그 툴바를 띄울 수 있는 것이다.
  - 혹은, 디버그 툴바를 띄울지 말지를 결정하는 로직을 만들고자 한다면 SHOW_TOOLBAR_CALLBACK 이라는 옵션을 구현하면 된다.

- 주의사항: 웹페이지의 template에 body element가 있어야만 디버그 툴바가 작동한다. 그 이유는 디버그 툴바의 html/script 디폴트 주입 타겟이 </body> 이기 때문이다.


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

- 

