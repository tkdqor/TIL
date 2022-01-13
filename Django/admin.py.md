# App별로 관리하는 admin.py
- 새로운 app를 만들고나서 admin 페이지에 관리하기 위해서는, app 내부에 있는 admin.py에서
```python
from django.contrib import admin
from .models import Post

# Register your models here.

admin.site.register(Post)
```
이런식으로 같은 디렉토리 안에 있는 models.py에서 Post 모델 클래스를 import하고, 밑에는 admin site에 Post 모델을 등록해주는 코드를 입력하면 된다.

- 그리고나서 어드민 페이지에 접속하면
<img src="https://user-images.githubusercontent.com/95380638/149297335-0a8fad0a-c263-41d2-8fab-ae7a380b996f.png" width="50%" height="50%">
이렇게 Post 모델이 등록된다.
