## Django 모델 필드
- 장고 모델은 데이터베이스 쿼리를 대신 생성해주고 실행하고 그 결과를 받아와서 가공해주는 역할의 ORM이다. 데이터베이스가 지원하는 기능을 모델을 통해 사용하는 것이다. 데이터베이스에서 지원하는 필드 타입에 따라 다양한 모델 필드가 지원되며, 특정 데이터베이스를 위한 모델 필드도 있다.
- 장고의 모든 기능을 지원하는 데이터베이스는 PostgreSQL이다. 강력한 위치검색쿼리가 필요하거나, PostgreSQL에서만 지원하는 모델필드가 필요할 경우에 검토해보자.
- 마이크로소프트의 MS-SQL 서버와 연동하고자 한다면, microsoft에서 mssql-django 라이브러리를 직접 개발하여 배포하고 있다.

### 기본 지원되는 모델 필드 타입
- 먼저 django의 공식 문서에 있는 필드에 대한 내용을 한 번 읽어보자. https://docs.djangoproject.com/en/3.2/ref/models/fields/#field-types

- primary Key: AutoField, BigAutoField
  - 기본적으로 모든 테이블에 id필드가 숨겨져 있는데, 그 필드가 AutoField이다. 숫자가 1씩 자동으로 증가하는 필드이다. 따라서 해당 필드를 우리는 default로 사용하게 된다.
- 문자열: CharField, TextField, SlugField
- 날짜/시간: DateField, TimeField, DateTimeField, DurationField
- 참/거짓: BooleanField, NullBooleanField
- 숫자: IntegerField, SmallIntegerField, PositiveIntegerField, PositiveSmallIntegerField, BigIntegerField, DecimalField, FloatField
- 파일: BinaryField, FileField, ImageField, FilePathField
- 이메일: EmailField
- URL: URLField
- UUID: UUIDField
- 아이피: GenericIPAddressField

### 관계에 대한 필드 타입
- ForeignKey(외래키) (1:N관계..?)
- ManyToManyField
- OneToOneField


- 우리가 만약 Posting을 저장하는 테이블을 하나 만들고, Tag를 저장하는 필드를 하나 만든다고 했을 때, Posting 테이블에 있는 레코드와 Tag 테이블에 있는 레코드의 관계를 명시할 수 있다. 위의 3가지의 관계를 지정할 수 있다.
- 그리고 기본 지원되는 필드 이외에, 다양한 커스텀 필드들이 있다.
  - django-model-utils: https://django-model-utils.readthedocs.io/en/latest/
  - 여기서 지원되는 필드들을 사용할 수 있다.


## 모델필드는 -> DB 필드타입을 반영
- DB에서 지원하는 필드들을 반영한다. 데이터베이스의 Varchar라는 필드타입이 있는데 이 필드타입에 매핑되는 장고모델 필드는 -> CharField, SlugField, URLField, EmailField 등이 있다.
- 그리고 python 데이터타입과 데이터베이스 데이터타입을 매핑해놓았다.
  - AutoField -> int
    장고 모델 중에 AutoField는 python에서 참조할 때 int가 된다.
  - BinaryField -> bytes
  - BooleanField -> bool
  - CharField / SlugField / URLField / EmailField -> str 
    - 이 4개 필드는 모두 문자열로 참조되는데, 차이점은 디폴트로 적용된 유효성 검사 로직 등의 차이가 있다. CharField는 단순히 지정 길이 안에서의 문자열을 저장 / URLField는 그 문자열이 URL 형식이어야지만 저장이 됨 / EmailField에는 이메일 형식이어야지만 저장이 되도록 설정됨

- 또한, 같은 모델필드라고 할지라도 DB에 따라 다른 타입이 될 수도 있다. DB에 따라 지원하는 기능이 다르다. 
  - 그래서 꼭, DB 엔진이 바뀌면 SQL를 확인하는 습관이 중요하다.


## 자주 쓰이는 필드 공통 옵션
- blank: 장고 단에서 validation 시에 empty 허용 여부(디폴트: false)
  - 예를 들어, 문자열의 경우에 빈 문자열을 허용하는 것. 이걸 생략하게 되면 false로 빈 문자열을 허용하지 않는다.
- null(DB옵션): null 허용 여부(디폴트: false)
- db_index(DB옵션): 인덱스 필드 여부(디폴트: false)
  - 모델에 db_index를 걸려고 한다면 이 옵션을 주고 migration을 하게 되면 적용이 된다. 근데 이미 데이터베이스를 꾸며놓았다면, 그 위에 장고의 이 옵션을 걸어도 쓸일이 없다. 이 옵션은 migration할 때 사용한다.
- default: 디플트 값 지정, 혹은 값을 리턴해줄 함수 지정 / 사용자에게 디폴트값을 제공하고자 할 때
- unique(DB옵션): 현재 테이블 내에서 유일성 여부(디폴트: false)
  - migration 할 때도 사용하고 장고 단에서 유효성검사를 수행할 때도 사용한다.
- choices: select 박스 소스로 사용
- validators: validators를 수행할 함수를 다수 지정
  - 모델 필드에 따라 고유한 validators들이 등록 (Ex. 이메일만 받기)
- verbose_name: 필드 레이블, 미지정 시 필드명이 사용
- help_text: 필드 입력 도움말
