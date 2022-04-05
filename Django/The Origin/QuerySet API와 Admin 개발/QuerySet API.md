## QuerySet API
- django에서는 model과 QuerySet API가 ORM역할을 해준다.
- **Query** : DB에 정보를 요청하는 것 또는 요청하는 행위 자체를 의미
- **QuerySet** : django에서 클래스로 정의되어있는 코드로, DB에서 전달받은 객체의 목록
- **QuerySet API** : DB에 요청하기 위한 인터페이스
  - 우리가 데이터베이스에 직접적으로 sql를 날리지 않기 때문에 인터페이스가 존재하게 된다. 그래서 우리가 어떤 함수를 호출했을 때 -> 어떤 sql문이 날라가게 되는지를 의미한다.
  - 관련 공식 문서 : https://docs.djangoproject.com/en/4.0/ref/models/querysets/


### QuerySet API 주요 함수
- **새 QuerySet를 반환하는 메서드**
  - filter() / exclude() - 어떤 데이터를 제외할 수 있음 / order_by() / select_related() / prefetch_related() / raw() - 직접 sql문을 작성해서 쿼리를 날릴 수 있게 해준다.
  - 공통적으로 여러개의 데이터 목록을 반환받는 함수들이다. 물론 하나도 되겠지만 여러개가 가능하다는 것이다.


- **QuerySet를 반환하지 않는 메서드**
  - get() - 테이블에서 하나의 row를 뽑아낼 수 있다. / create() / count() - 특정 데이터가 몇개인지 숫자를 반환해주는 함수. / first() - 가장 첫번째 데이터 / last() - 가장 마지막 데이터
  - 공통적으로 데이터 하나만 받던가 또는 숫자를 받는 등, 목록의 형태의 QuerySet이 아니다.
