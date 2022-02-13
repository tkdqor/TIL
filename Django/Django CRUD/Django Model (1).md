## Django Model
- 유저 정보나 게시물 정보 등 웹서비스에 필요한 다양한 정보들을 체계적으로 저장하고, 저장되어 있는 데이터를 조회할 수 있도록 하는 데이터의 집합을 데이터베이스(Database), DB라고 한다.
  - 이러한 데이터베이스를 만드는 것부터 시작해서 여러 사람들이 이 데이터베이스에 접근하여 새로운 데이터를 추가하거나 기존 데이터를 조회할 수 있게끔 도와주는 용도로 만든 소프트웨어를 Database Management System, DBMS라고 부른다. 데이터베이스를 다루기 위한 소프트웨어라고 생각하면 된다.
- 현재 현업에서 가장 많이 쓰이는 종류의 데이터베이스는 Relational Dadtabase, 관계형 데이터베이스라고 할 수 있다. 이러한 관계형 데이터베이스를 위한 DBMS로 Oracle, MySQL, PostgreSQL 등이 있다.


### 관계형 데이터베이스(Relational Dadtabase)
- **관계형 데이터베이스에서는 모든 데이터를 2차원 테이블 형태로 표현한다.**
- 이러한 RDB를 다루기 위한 소프트웨어가 바로 RDBMS(Relational Database Management System)이라고 한다. ex) Oracle, MySQL, PostgreSQL
  - 그리고 이러한 RDBMS에서 데이터를 다루기 위해 설계된 언어가 바로 SQL(Structured Query Language)이다. 데이터베이스에서 데이터를 다룰 때에는 반드시 이 SQL를 사용해야 한다. 
  - 그래서, 우리가 개발하는 웹 서비스의 데이터를 생성 및 조작하려면 SQL를 알아야 하는데 python에서는 굳이 SQL를 알고 있지 않아도 데이터베이스를 관리할 수 있다.


### ORM(Object Relational Mapping)
- SQL를 알지 못해도, python 클래스를 통해서 모델 테이블의 구성을 정의하고 / 해당 클래스의 인스턴스를 기반으로 새로운 데이터를 추가하거나 수정 및 삭제를 할 수 있다. 
  - 우리가 ORM을 기반으로 python 코드를 작성하게 되면 -> SQL로 자동 변환을 해주어서 해당 SQL를 실행하여 데이터베이스를 관리할 수 있게 된다.

- ORM의 또 다른 장점은 RDBMS를 가리지 않는다는 것이다. ORM를 사용하면 작성한 python 코드를 현재 ORM에 연결되어있는 RDBMS에 맞춰서 적절한 SQL로 변환해주기 때문에 특정 RDBMS에 종속되지 않고 개발할 수 있다.


### Django models.py
- django 모델 역시 app 단위로 쪼개서 관리한다. posts 앱의 models.py에 다음과 같이 입력하자.
```python
from django.db import models

# Create your models here.

class Post(models.Model):
    
    author = models.CharField(max_length=100)
    body = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f'{self.author}: {self.body}'

```

- django model에서는 우리가 저장하고자 하는 데이터가 어떠한 속성과 기능들을 가지는지를 python 클래스를 통해서 정의한다. 그래서 클래스를 이용해서 모델을 정의하게 된다.
  - 게시물과 관련된 데이터를 위해 Post라는 이름으로 클래스를 정의.

- django에서 새로운 데이터를 관리하기 위해 model 클래스를 생성할 때는 **from django.db import models** 이렇게 models 모듈을 import 해야한다. 그리고 이 models 모듈 내부에 -> Model이라는 클래스가 있어서 이 Model 클래스를 상속받는 클래스로 설정해줘야 한다.

- 클래스의 이름을 정했다면, 그 내부에 속성, 어트리뷰트를 작성해준다. 즉, 해당 모델의 데이터베이스 테이블에 필요한 column의 이름을 설정하는 것이다. ex) author, body, created_at
  - 그리고 이 속성마다 models.CharField, models.TextField와 같이 필드를 설정한다. 이러한 설정은 각 column이 데이터베이스에 저장될 때 -> 어떠한 data type으로 저장되어야 하는지를 결정한다.
  - CharField는 길이가 제한되어있는 짧은 텍스트 데이터를 저장할 때 사용. max_length를 이용해 길이를 제한할 수 있다.
  - TextField는 대용량의 텍스트 데이터들을 저장하고 싶을 떄 사용하는 필드이다.
  - DateTimeField는 날짜와 시간을 저장하고 싶을 때 사용. DateField는 날짜만 저장하고 싶을 때 사용.
