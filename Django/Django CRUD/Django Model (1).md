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
    - DateTimeField의 auto_now_add=True 를 설정하면, DB에 처음 데이터가 생성될 때 딱 한번 자동으로 현재시간이 설정된다.

- **클래스 내부에 던더str 메소드는, 해당 메소드를 설정하지 않으면 model의 아이디 정보만 출력되고 내용물을 확인할 수 없다.** 해당 인스턴스가 어떤 어트리뷰트를 포함하고 있는지 간단하게 확인할 수 있게 설정하는 것이다. **그래서 Post 클래스의 인스턴스가 출력될 때 -> author와 body 정보가 출력될 수 있도록 위의 코드에서 설정했다.** 


- models.py 내용을 설정한 이후에는 terminal에
```python
python manage.py makemigrations posts(app이름)

```

- 이렇게 입력하면 해당 model 파일을 기반으로 migration 파일을 새롭게 생성해준다. 
  - migration 파일은 우리가 작성한 model 파일, model 클래스를 데이터베이스에 실제로 반영하기 위해서 어떠한 일들을 해야하는지 시간 순서대로 기록해놓은 파일이다. 아직은 실제 데이터베이스에 반영되지는 않았다.
  - 해당 파일을 보면, 우리가 작성한 model 클래스가 실제로 데이터베이스에 반영되기 위해서 어떠한 절차를 수행해야 하는지 그 내용을 operations라는 리스트로 나열되어있다.

- python manage.py sqlmigrate posts 0001 라는 코드를 입력하면 -> posts app 내부에 있는 0001, 첫번째 migration 파일이 어떠한 SQL 코드로 변환될지를 분석해달라는 의미이다.

```terminal
(env) kimsangbaek@gimsangbaeg-ui-MacBookAir heestagram % python manage.py sqlmigrate posts 0001
BEGIN;
--
-- Create model Post
--
CREATE TABLE "posts_post" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "author" varchar(100) NOT NULL, "body" text NOT NULL, "created_at" datetime NOT NULL);
COMMIT;
``` 

- 그래서 위와같이 실제로 수행될 3줄의 sql 코드를 우리가 확인할 수 있다. 
- **migration 파일들은 모델의 변경 히스토리를 파악할 수 있게 해준다.** 추후에 우리가 Post 라는 모델에 다른 column를 추가한 다음, migration을 진행하면 -> django가 분석해서 실제 데이터베이스에 있는 테이블의 형상과 우리가 수정한 Post model 클래스의 형상을 비교해서, 실제 데이터가 저장되어 있는 데이터베이스에서는 3개의 필드밖에 없는데 새롭게 작성된 model 클래스에는 또 다른 필드가 생겼다는 것을 확인한다. 
  - **그래서, 데이터베이스에 새로운 필드를 추가하려면 어떠한 sql 코드가 필요한지를 먼저 찾아내고 그 sql 코드에 대해서 설명하는 것을 python 코드로 하나의 히스토리로 남기는 것이 migration 파일이다.**


+ django 프로젝트를 처음 시작하고 server를 구현 할 때마다 You have 18 unapplied migration(s)... 라는 메세지가 뜨게 되는데, django가 내부적으로 사용하고 있는 다양한 app들이 있고 거기에도 다양한 migration 파일들이 있다. 아직 실제로 반영이 되지 않았다고 하니까 반영을 해줘야 한다는 경고이다.

```terminal
python manage.py migrate 

```

- migration 파일을 근거로 해서 migration 파일 내부에 작성되어있는 operations, 수행해야 할 절차들을 실제로 수행하는 것이 migrate이다. 그래서 django 내부에 있는 app들의 migration 파일과 우리가 만든 posts app의 migration 파일도 실제 데이터베이스에 반영이 되었다. 
  - 그래서 최종적으로, 우리가 작성한 model 클래스와 실제 연결되어 있는 데이터베이스 내부의 구조가 완벽하게 일치된다.

```terminal
python manage.py showmigrations posts

posts
 [X] 0001_initial

```

- 마지막으로 위와 같이 터미널에 입력하면, posts App의 migration 파일이 실제로 적용되었는지 X 표시로 확인해볼 수 있다.

- 이렇게 model 클래스가 수정될 때 마다 위와같은 과정을 반복하게 된다.
  - 프로젝트 처음 시점부터 현재까지 어떤 데이터 및 모델이 추가되었는지 변경된 히스토리가 다 migration 파일로 남아있게 된다.
  - 우리가 기존에 사용하는 데이터베이스를 버리고 새로운 데이터베이스를 연동한다고 하더라도, 이 히스토리가 남겨져 있기 때문에 과거부터 현재까지의 migration 파일을 순서대로 실행한다면 -> 데이터베이스가 비어있더라도 기존에 사용하던 데이터베이스와 완전히 동일한 형태로 재구축을 할 수 있다.
