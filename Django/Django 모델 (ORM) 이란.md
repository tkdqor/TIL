## 데이터베이스와 SQL
- 데이터베이스의 종류
  - 데이터베이스는 별도의 서버로, SQL 또는 쿼리라는 언어를 통해서 질의를 할 수 있다.
  - 데이터베이스의 종류로는 RDBMS(관계형 데이터베이스 관리 시스템) (예를 들어 PostgreSQL, MySQL, SQLite, MS-SQL, Oracle 등) 이 있고,     
    NoSQL이 있다.(예를 들어 MongoDB, Cassandra, CouchDB, Google BigTable 등)
  - 둘다 SQL를 지원하는데, NoSQL의 경우에는 해당 엔진만의 SQL를 지원할 수 있다.

- 데이터베이스에 쿼리하기 위한 언어가 바로 SQL
  - 같은 작업을 하더라도 보다 적은 수의 SQL, 보다 높은 성능의 SQL이 필요하다.
  - 직접 SQL를 만들어서 사용할 수 있지만, 그리고 수많은 애플리케이션 개발 조직에서는 직접 SQL를 만들어서 개발을 했을 것이다. 그렇지만 요즘에는, ORM(Object-relational mapping)을 통해 SQL를 생성/실행하기도 한다. 이걸 활용하면 생산성이 높아진다. 
  - **중요한 건, ORM를 쓰더라도 내가 작성한 ORM코드를 통해 어떤 SQL이 실행되고 있는지 파악을 하고 이를 최적화할 수 있어야 한다. -> 이를 위해 django-debug-toolbar를 활용하자.**


## 장고 ORM인 모델은, RDB만 지원
- 장고에서 기본 지원하는 ORM으로 장고 모델이 있다. 장고에서는 모델이 모든 프로세스의 핵심이다. 비즈니스에 맞춰 모델을 잘 설계하는 것이 장고 개발의 절반이라고 해도 과언이 아니다.
- 장고 3.0.2 기준으로 기본적으로 제공되는 백엔드(RDBMS)가 mysql / oracle / postgresql / splite3 가 있다. 
- https://www.slideshare.net/TaehwanKim27/sql-server-django-118638328 마이크로소프트 관련 해당 자료 보기.


### 다양한 python ORM이 있다.
- Relational Databases : 장고 모델 말고도, SQLAlchemy, Orator, Peewee, PonyORM 등
- NoSQL Databases : djnago-mongodb-engine, hot-redis, MongoEngine, PynamoDB 등
- 장고를 사용한다고 해서 장고 모델만 사용할 수 있는 건 아니다. 장고 모델과 함께 지원하는 기능이 많다.


### 장고의 최고 강점은, Model과 Form
- 장고에서는 다양한 ORM, 라이브러리를 사용할 수 있고 Model과 Form이 강력하다. 장고 개발을 한다고 하면 이 2개를 이용한 개발을 의미한다.
  - 이걸 활용해서 우리는 수월하게 데이터베이스 처리를 할 수 있고, 유저가 서버로 올려보낸 값에 대한 유효성 검사 및 DB 저장 등도 수월하게 할 수 있다. 
- Model를 통해 SQL를 수행하게 되는데, SQL를 직접 실행할 수도 있지만 가능하면 ORM를 사용하자. 직접 SQL문자열을 조합하지말고, 인자로 처리하자.


### 직접 SQL로 실행해보기
```terminal
python manage.py shell
```

- 프로젝트에 있는 상태에서 shell 사용해보기.

```terminal
>>> from django.db import connection
>>> cursor = connection.cursor()     커서는 커넥션의 커서.. 커서를 획득.
>>> cursor.close()                   획득한 커서를 닫고.. 
>>> exit()                           shell에서 빠져나오기
```


* * *
## Django 모델 설계해보기
- **먼저 데이터베이스 테이블(즉, 실제 DB를 의미. sqlite3나 mySQL 등등)과 python 클래스를 1:1로 매핑.(models.py안에 클래스로 정의한다는 것)**
- 모델 클래스명은 **단수형**으로 지정. ex) Posts(x) -> Post(o)
  - 그리고 **클래스이기에 첫글자는 무조건 대문자로 지정 / PascalCase 네이밍 -> 즉, 모든 단어의 첫글자가 대문자가 되게끔 설정. ex) SelectDetailBoard 등..**
- **매핑되는 모델 클래스는 DB 테이블 필드 내역이 일치해야한다. models.py에 있는 필드와 실제 DB 테이블에 있는 필드가 일치해야 오류없이 작동할 수 있다.**
  - DB 테이블을 먼저 만들수도 있고, python 클래스를 먼저 만들고 DB 테이블을 맞출 수도 있다. DB 테이블이 이미 만들어진 조직도 있다. 그렇다면 그 이후에 내역에 맞춰서 python 클래스를 만들어줘야 한다. 만약, python 클래스를 먼저 만들었다면 이 클래스에 맞춰서 DB 테이블을 자동생성할 수 있도록 할 수도 있다. -> 이 기능이 바로 **Database migration기능** 이다.
- 모델을 만들기 전에는, 서비스에 맞게 데이터베이스 설계가 필수이다. 이는 데이터베이스의 영역이다. -> 그래서 **django와 별도로 관계형 데이터베이스에 대한 학습이 필요하다.**

- **python 클래스를 정의할 때에는, app 디렉터리에 있는 models.py에서 정의하면 된다.**
```python
from django.db import models     # models 패키지 import

class Post(models.Model):        # 괄호 부분은 python의 상속 문법이다. models 패키지의 Model 클래스를 상속받는 것이다.
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

- 이렇게 models.py에서 정의해주면 된다. class ~ 라고 시작하는 첫번째 줄은 models 패키지의 Model 클래스를 상속받아서 새롭게 Post라는 클래스를 정의하는 것이다.
  - 그 밑으로는 원하는 필드명 / 그리고 필드에 대한 타입을 설정하게 된다. 모델에서 타입별로 실제 DB마다의 필드 타입이 결정된다.
- 위의 코드처럼 입력하고 이 내역대로 실제 DB 테이블이 구성되어 있다면, 우리는 모델만으로도 DB 작업을 수행할 수 있다. 


### 모델 활용 순서
- **장고 모델을 통해 데이터베이스 형상을 관리할 경우**
  - 먼저 모델 클래스를 작성
  - 모델 클래스로부터 마이그레이션 파일 생성 -> makemigrations 명령
  - 마이그레이션 파일을 데이터베이스에 적용 -> migrate 명령
  - 과정이 완료되면 모델을 활용할 수 있음


- **장고 외부에서, 이미 장고전에 데이터베이스를 구축해놓은 경우**
  - 이러한 경우에는 inspectdb 명령을 통해서 소스파일들을 만든 다음에, copy해서 사용하면 된다. 즉, 데이터베이스로부터 모델 클래스 소스를 생성하는 것이다.


### 모델명과 DB 테이블명
- DB 테이블명의 경우, 디폴트가 "앱이름_모델명" 이다.
  - ex) blog앱에서 Post모델은 -> "blog_post"    /  blog앱에서 Comment모델은 -> "blog_comment"
  - ex) shop앱에서 Item모델은 -> "shop_item"    /  shop앱에서 Review모델은 -> "shop_review"  

- 커스텀 지정을 하고자 한다면, 모델 Meta 클래스의 db_table 속성에서 원하는 테이블명을 makemigrations 하기전에 적용을 하면 된다.


* * *
## 새로운 app과 model 만들어보기
```terminal
python manage.py startapp instagram
```

- 앱을 이렇게 만들고 settings.py에서 app를 추가해주자. 그리고 앱 내부에 urls.py를 생성해준다. 또한, 프로젝트 디렉터리 내부에 있는 urls.py에 가서  
```python
from django.contrib import admin
from django.urls import path, include      # include를 새롭게 추가

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog1/', include('blog1.urls')),   # 새롭게 url를 만들어주고 해당 url은 blog1 앱의 urls.py로 보내주기
    path('instagram', include('instagram.urls')),  # 새롭게 url를 만들어주고 해당 url은 instagram 앱의 urls.py로 보내주기
]
```

- 다음과 같이 url를 추가한다.

- 그 다음에 이제 models.py에 들어가서 클래스를 정의하자.
```python
from django.db import models


class Post(models.Model):       # 우리가 원하는 데이터베이스에 저장하고 싶은 내역대로 설계를 해서 사용하면 된다.
    message = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
```

- 인스타그램을 생각해서 간단하게 클래스를 정의하고나서 python manage.py makemigrations instagram 입력 / python manage.py migrate instagram 입력
  - 이렇게 입력하게 되면, 실제로 DB(데이터베이스)에 "instagram_post"라는 이름의 테이블이 생성된 것이다.

```terminal
python manage.py sqlmigrate instagram 0001_initial
```

- 그리고 이렇게 입력하게 되면, 

```terminal
BEGIN;
--
-- Create model Post
--
CREATE TABLE "instagram_post" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "message" text NOT NULL, "created_at" datetime NOT NULL, "updated_at" datetime NOT NULL);
COMMIT;
```

- **이런식으로 뜨게 되는데, 실제로 데이터베이스에 들어가는 쿼리문을 우리가 확인해볼 수 있다.(migrate했을 때 입력한 쿼리문이라고 생각하면 된다!)**
- 
- **CREATE TABLE "instagram_post" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "message" text NOT NULL, "created_at" datetime NOT NULL, "updated_at" datetime NOT NULL);** 이러한 쿼리문이 실행되었다. 
  - instagram_post라는 


- 실제 SQL로 테이블을 생성하는 구문과 비교해도 똑같이 나온다. 참고 블로그 https://jhnyang.tistory.com/307

