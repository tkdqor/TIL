### 새로운 필드가 필수필드라면?
- 필수필드 여부 : blank / null 옵션이 모두 false일 때 (디폴트이다)
- 필수필드는 처음에 특정 모델에 대한 migration을 처음 적용할 때는 별 문제가 없다. 그런데 추후에 migration이 끝났고 새로운 필드를 추가하고자 할 때, 그 필드가 필수필드라면 makemigrations 명령을 수행할 때
  기존 record들에 어떤 값을 채워넣을 지 묻게된다.
  - ex) 만약 Post 모델이 있는데, 이미 title / content / created_at / updated_at과 같은 필드들이 이미 생성이 되어 있고 migrate가 다 된 상태이다. 이 때, 추가로 만약에 author라는 필드를 추가하려고 한다면 꼭 필요하니까 
    필수필드로 지정하게 되는데, 이렇게 지정하고 나서 makemigrations를 하게 될 때를 생각해보자.
  
```python
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

```terminal
python manage.py makemigrations blog1


You are trying to add a non-nullable field 'author' to post without a default; we can't do that (the database needs something to populate existing rows).
Please select a fix:
 1) Provide a one-off default now (will be set on all existing rows with a null value for this column)
 2) Quit, and let me add a default in models.py
Select an option: 
```

- 이 상태에서 makemigrations를 하게 되면, 위와 같이 물어보게 된다. 
  - 여기서 2번을 선택하게 되면 종료가 되고 우리가 author 필드 속성에 default=1 이렇게 디폴트 값을 주거나 / null=True를 해주게 되면 질문이 나오지 않게 된다.
  - 즉, 어떤 값을 꼭 채워넣어야 하는 필드인데 / 이미 Post라는 테이블이 구성이 되어있는데 새로운 author 필드를 추가할 때에 기존에 record에게 author 필드에 대해서 어떤 값으로 채워줘야 하느냐를 묻게 되는 것이다.
  - 1번은 지금 그 값을 입력하겠다는 것이고 / 2번은 명령 수행을 중단하고 우리가 default등 속성을 정해줘야 하는 것이다.
    - 1번은 입력하면, 

```terminal
Please enter the default value now, as valid Python
The datetime and django.utils.timezone modules are available, so you can do e.g. timezone.now
Type 'exit' to exit this prompt
>>> 
```

- 이 때 입력하는 값은 필드 타입에 맞게 값을 지정해줘야 한다는 것이다.
  - **지금은 author 필드가 ForeignKey(1:N관계)로 설정되어 있으므로 -> author 필드는 User 모델에 대한 pk값이 들어가야 된다는 것이다.**
  - 현재 User 모델은 Primary Key가 1,2,3,4.. 이렇게 설정되어 있으니까 author 필드 역시 1 / 2/ 3 ... 이렇게 유저1번 또는 유저2번 이렇게 숫자로 입력을 해주어야 한다.

- 만약, 추가하는 필드의 필드 타입이 CharField라면 문자열을, IntegerField면 정수를, DataTimeField면 DataTime을 입력해야 된다는 것이다.
  - 이걸 잘 모르고 그냥 위의 terminal 설명의 예시처럼 뜬금없이 timezone.now 를 입력하면 migrations가 되긴 된다. 파일이 생성은 된다.
    - 하지만 그 다음에 python manage.py migrate blog1 이렇게 입력하면 -> 오류가 나오게 된다. 결론은 실제로 적용이 되지 않는 것이다. 즉, makemigraitons는 실제 유효성 검사를 하지 않고 입력된 값으로 마이그레이션 파일만 만들어주는 것이다. 

- 이렇게 타입이 맞지 않는 경우 sqlite에서는 오류가 나지 않지만, 실제 production DB에다가 migrate 할 때는 오류가 날 수 있다. 그래서 타입에 정확히 맞춰서 입력해야 된다.
  - sqlite는 생각보다 너그러운 DB이다. 실제 production에서 사용하는 DB는 까다로운 DB이다. 그래서 이런 부분을 미리 아는 사람들은 개발환경에서도, DB를 실제 production에서 사용하는 DB와 동일한 엔진의 DB를 사용하는 것이 좋을 수도 있다.
  - production DB 설정이 힘들다면, Docker compose를 활용하면 DB 세팅이 쉬워진다. 


- 이제 실제로 위의 terminal인 
```terminal
Please enter the default value now, as valid Python
The datetime and django.utils.timezone modules are available, so you can do e.g. timezone.now
Type 'exit' to exit this prompt
>>> 
```
- 여기에서 1을 누르고 enter를 하면 -> Post 모델의 author 필드는 디폴트 값으로 User 모델의 1번 pk로 지정이 된다.(즉, 1번 유저가 작성한 글이 된다.)
- 이 ForeignKey를 나중에 만들일은 없을 것이다. 게시글이 10만개라면 10만개 글의 author_id 라는 필드에는 전부 1이 들어가게 된다. 그래서 이렇게 1로 넣고나서 나중에 변경할 수 있다.
  - **아니면, 처음에는 null과 blank를 True로 주고 -> 데이터를 전부 채운다음 null과 blank를 False로 바꾸어서 migration/migrate를 진행하는 방법도 있다.**


- 추가로, 만약 makemigrations를 할 때 새롭게 추가되는 필수필드가 3개라면 위의 질문이 연속해서 3번 뜨게 된다. 그래서 질문속에 물어보는 필드가 어떤건지 잘 참고해서 마이그레이션 파일을 생성하면 된다.

* * *

### 협업 팁
- 절대 하지 말아야 할 일 : 팀원 각자가 마이그레이션 파일을 생성하게 되면 충돌하게 된다. 
  - 만약 우리가 하나의 django 프로젝트에서 개발자가 3명이고, 하나의 저장소에서 각각 clone를 진행한 다음, A가 먼저 모델을 만들고 마이그레이션까지 다 하고 commit를 해버린다. 그리고 B와 C도 똑같이 해버린다면, 충돌이 발생하게 되는 것이다.

- 그래서 추천되는 방법은, 마이그레이션 파일 생성은 1명이 전담해서 생성하는 것이다. A가 makemigrations까지 진행하고 commit / push까지 진행한 다음 -> B와 C는 각각 pull해서 migrate만 하면 된다.
  - 보통 개발 단계에서는 DB를 따로 가져가기 때문이다. 


### 추가 Tip
- 개발 시에 "서버에 아직 반영하지 않은" 마이그레이션을 다수 생성했다면 이를 그대로 서버에 반영(migrate)하지 말고 하나의 마이그레이션으로 합쳐서 적용하기를 권장.
  - ex) 우리가 migration 파일을 0001, 0002, 0003, 0004번이 있는데 만약 3번까지 서버에 올라간 상태(migrate)이고 / 조금 수정해서 0004번 makemigrations, 0005번 makemigrations, 0006번 makemigrations -> 이렇게 마이그레이션 파일을 3개 만들었다고 하자. 이 때 python manage.py migrate 0003을 해서 4,5,6을 취소시킨다. 그 다음에 4,5,6번 마이그레이션 파일을 지운다. 지금 상황에서 마이그레이션 파일을 지워도 모델 내역에는 4,5,6번이 반영된 것으로 남아있다. 그래서 지운 다음에 다시 python manage.py makemigrations를 진행하면, 깔끔하게 1개의 파일로서 마이그레이션 파일이 생성된다. 

- 혹은 미적용 마이그레이션 파일들을 하나로 합치기 위해 squashmigrations 명령을 사용해볼 수도 있다.
