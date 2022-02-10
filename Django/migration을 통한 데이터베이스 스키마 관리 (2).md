### 새로운 필드가 필수필드라면?
- 필수필드 여부 : blank / null 옵션이 모두 false일 때 (디폴트이다)
- 필수필드는 처음에 특정 모델에 대한 migration을 처음 적용할 때는 별 문제가 없다. 그런데 추후에 migration이 끝났고 새로운 필드를 추가하고자 할 때, 그 필드가 필수필드라면 makemigrations 명령을 수행할 때
  기존 record들에 어떤 값을 채워넣을 지 묻게된다.
  - ex) 만약 Post 모델이 있는데, 이미 title / content / created_at / updated_at과 같은 필드들이 이미 생성이 되어 있고 migrate가 다 된 상태이다. 이 때, 추가로 만약에 author라는 필드를 추가하려고 한다면 꼭 필요하니까 
    필수필드로 지정하게 되는데, 이렇게 지정하고 나서 makemigrations를 하게 될 때를 생각해보자.
  
```terminal
from django.conf import settings
from django.db import models     # models 패키지 import

class Post(models.Model):        # 괄호 부분은 python의 상속 문법이다. models 패키지의 Model 클래스를 상속받는 것이다.
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, related_name='+')
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

- 먼저 Post 모델에 새롭게 author라는 필드를 추가했다. related_name='+'은 instagram앱의 Post 역시 1:N관계를 User 모델과 설정하여 blog1앱의 Post 모델 이름이 같아 reverse name이 충돌해서 그렇다. 그래서 여기서는 포기하기 위해 이렇게 설정했다.
- author라는 필드에 blank=False 또는 null=False라고 따로 설정하지 않아도 디폴트로 설정되어있다.
