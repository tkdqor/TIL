## QuerySet API
- django에서는 model과 QuerySet API가 ORM역할을 해준다.
- **Query** : DB에 정보를 요청하는 것 또는 요청하는 행위 자체를 의미
- **QuerySet** : django에서 클래스로 정의되어있는 코드로, DB에서 전달받은 객체의 목록
- **QuerySet API** : DB에 요청하기 위한 인터페이스
  - 우리가 데이터베이스에 직접적으로 sql를 날리지 않기 때문에 인터페이스가 존재하게 된다. 그래서 우리가 어떤 함수를 호출했을 때 -> 어떤 sql문이 날라가게 되는지를 의미한다. 이러한 QuerySet API 덕분에 우리가 직접 sql문을 작성하지 않아도 대신 sql문을 작성해서 DB에 호출해준다.
  - 관련 공식 문서 : https://docs.djangoproject.com/en/4.0/ref/models/querysets/


### QuerySet API 주요 함수
- **새 QuerySet를 반환하는 메서드**
  - filter() / exclude() - 어떤 데이터를 제외할 수 있음 / order_by() / select_related() / prefetch_related() / raw() - 직접 sql문을 작성해서 쿼리를 날릴 수 있게 해준다.
  - 공통적으로 여러개의 데이터 목록을 반환받는 함수들이다. 물론 하나도 되겠지만 여러개가 가능하다는 것이다.
  - order_by()의 경우, .order_by('-pub_date', 'headline') 이렇게 2개의 필드를 설정할 수도 있다.


- **QuerySet를 반환하지 않는 메서드**
  - get() - 테이블에서 하나의 row를 뽑아낼 수 있다. / create() - 데이터베이스에 데이터를 추가할 수 있다. / count() - 특정 데이터가 몇개인지 숫자를 반환해주는 함수. / first() - 가장 첫번째 데이터 / last() - 가장 마지막 데이터
  - 공통적으로 데이터 하나만 받던가 또는 숫자를 받는 등, 목록의 형태의 QuerySet이 아니다.
  - **get_or_create()** 라는 함수도 있다. 데이터를 조회를 할 때 있으면 그냥 조회가 되는 것이고, 없으면 데이터를 생성해주는 함수이다. 그리고 그냥 조회가 되면 False, 생성되면 True를 반환해준다.


```python
try:
    obj = Person.objects.get(first_name='John', last_name='Lennon')
except Person.DoesNotExist:
    obj = Person(first_name='John', last_name='Lennon', birthday=date(1940, 10, 9))
    obj.save()
```

```python
obj, created = Person.objects.get_or_create(
    first_name='John',
    last_name='Lennon',
    defaults={'birthday': date(1940, 10, 9)},
)
```

- 위의 예외처리한 코드와 밑의 코드는 동일한 의미를 지니게 된다.


- 비슷하게 **update_or_create()** 라는 함수도 있다. 데이터가 있으면 update 시키고 데이터가 없으면 새로 생성하는 것이다. 

- 이러한 코드들을 사용했을 때 더 간결하고 생산성이 올라간다.


### Django Shell
- python이 인터프리터 언어이고 대화형 기반의 언어이기 때문에 우리가 터미널에서 python이라고 입력했을 때, 내가 입력하고 python이 응답해주고 이러한 대화 형식의 코드를 입력해볼 수 있다. 그런데 django에서도 python이기 때문에 이러한 것을 사용할 수 있다.
- 근데 django에서는 기본적으로 필요한 변수들이 있는데, 그냥 python이라고 코드를 입력하고 들어가게 되면 -> django를 찾지 못한다. django에 필요한 변수들이 들어가야 하기 때문에 

```terminal
python manage.py shell
```

- 위와 같은 명령어를 입력해야 python 인터프리터에 접속이 된다. 그리고 django에 있는 변수들을 가져와서 사용할 수 있다. 즉, model를 import해서 사용할 수 있다.


### Database Tool
- 데이터베이스에 관리를 위한 도구
- 툴을 활용하여 테이블이나 데이터를 조작할 수 있음
- GUI(Graphical User Interface) 제공
