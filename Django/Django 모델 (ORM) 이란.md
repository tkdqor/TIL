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
- 장고 3.0.2 기준으로 기본적으로 제공되는 백엔드(RDBMS)가 mysql / oracle / postgresql / splite3 가 있다. 
- https://www.slideshare.net/TaehwanKim27/sql-server-django-118638328 마이크로소프트 관련 해당 자료 보기.


### 다양한 python ORM이 있다.
- Relational Databases : 장고 모델 말고도, SQLAlchemy, Orator, Peewee, PonyORM 등
- NoSQL Databases : djnago-mongodb-engine, hot-redis, MongoEngine, PynamoDB 등
- 장고를 사용한다고 해서 장고 모델만 사용할 수 있는 건 아니다. 장고 모델과 함께 지원하는 기능이 많다.


### 장고의 최고 강점은, Model과 Form
- 장고에서는 다양한 ORM, 라이브러리를 사용할 수 있고 Model과 Form이 강력하다. 장고 개발을 한다고 하면 이 2개를 이용한 개발을 의미한다.
  - 이걸 활용해서 우리는 수월하게 데이터베이스 처리를 할 수 있고, 유저가 서버로 올려보낸 값에 대한 유효성 검사 및 DB 저장 등도 수월하게 할 수 있다. 
- Model를 통해 SQL를 수행하게 되는데, 

## Django 모델
- 장고에서 기본 지원하는 ORM으로 장고 모델이 있다. 장고에서는 모델이 모든 프로세스의 핵심이다. 비즈니스에 맞춰 모델을 잘 설계하는 것이 장고 개발의 절반이라고 해도 과언이 아니다.


