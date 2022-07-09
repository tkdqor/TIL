# Django

📖 **Contents**

- Django
  - [values 메서드](#values-메서드)
  - [annotate와 aggregate 메서드](#annotate와-aggregate-메서드)
  - [Lazy-loading이란](#lazy-loading이란)
  - [django signal이란](#django-signal이란)
  - [render와 redirect 관련](#render와-redirect-관련)
  - [DRF란](#drf란)
  - [djangorestframework-simplejwt란](#djangorestframework-simplejwt란)

* * *

## values 메서드
- **values** : iterable로 사용될 때 모델 인스턴스가 아닌 사전을 반환하는 QuerySet를 반환
  - [공식 문서](https://docs.djangoproject.com/en/4.0/ref/models/querysets/#django.db.models.query.QuerySet.values)
```python
# This list contains a Blog object.
>>> Blog.objects.filter(name__startswith='Beatles')
<QuerySet [<Blog: Beatles Blog>]>

# This list contains a dictionary.
>>> Blog.objects.filter(name__startswith='Beatles').values()
<QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>
```

- **values() 메서드에 특정 필드를 지정하면 각 사전에는 지정한 필드에 대한 필드 키/값만 포함된다.**
```python
>>> Blog.objects.values()
<QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>
>>> Blog.objects.values('id', 'name')
<QuerySet [{'id': 1, 'name': 'Beatles Blog'}]>
```

<br>

## annotate와 aggregate 메서드

- **annotate** : 엑셀에 column를 하나 추가하는 것과 같이 필드 하나를 만들고 거기에 다른 필드의 값을 그대로 복사하거나, 다른 필드의 값들을 조합한 값을 넣을 수 있게 해주는 메서드이다.
  - annotate의 사전적 의미는 '주석을 달다'라는 것으로 django에서는 주석 대신 필드를 추가한다고 생각하면 된다.

<br>

- **aggregate** : django에서 한 필드 전체의 합이나 평균, 개수 등을 계산할 때 사용할 수 있는 메서드이다.
  - aggregate의 사전적 의미는 '합계', '종합'이다.
  - **아래와 같이 딕셔너리 형태로 출력해준다.**

```python
# OrderLog 모델의 price라는 필드의 모든 값을 더해주기
>>> OrderLog.objects.all().aggregate(total_price=Sum('price'))
{'total_price': 262200}
```

- [관련 블로그](http://raccoonyy.github.io/django-annotate-and-aggregate-like-as-excel/)
- [관련 블로그2](https://tech.toktokhan.dev/2021/04/26/django-query-func/)


* * *

## Lazy-loading이란
- Django의 ORM은 다른 ORM과 마찬가지로 Lazy-loading방식을 사용한다. **Lazy-loading이란 python 코드로 ORM에서 명령을 실행할 때마다 데이터베이스에 접근하여 데이터를 가져오는 것이 아닌 모든 명령처리가 끝나고 실제 데이터를 불러와야할 때 데이터베이스 Query문을 실행하는 방식을 말한다.**
  - 반대로 **Eager-loading(즉시 로딩)은 Lazy-loading의 반대 개념이다. Lazy-loading은 Query문을 하나, 하나 실행하여 데이터를 가져온다면 Eager-loading은 지금 당장 사용하지 않을 데이터도 포함하여 Query문을 실행**하기 때문에 밑에 설명할 Lazy-loading의 N+1문제의 해결책으로 많이 사용하게 된다. Django에서 Eager-loading을 실행하는 방법은 select_related 메소드와 prefetch_related 메소드를 사용한다. 

- 우리가 ORM으로 쿼리문을 만들면 바로 데이터베이스에 Hit가 되는 것이 아니라, template에서 {% for item in recommendations %}  또는 {{ item.restaurant.category.name }} 이러한 코드가 실행될 때 마다 데이터베이스에 Hit를 치게 된다. 이게 바로 “lazy-loading 방식”이다. 그 다음 바로 이어서 {{ item.restaurant.main_image.image }} 해당 코드가 실행될 때는, 이미 메모리에 불러온 restaurant이 있기 때문에 Hit 하지는 않는다. 
  - **Lazy-loading의 성능이슈인 N+1 Query 문제는 외래키(Foreign Key)를 참조해서 데이터를 가져올 때 발생한다.** {% for item in recommendations %} 이러한 코드가 있고 그 밑에 {{ item.restaurant.category.name }} 이렇게 코드가 있을 때, 데이터 개수인 N번 Hit 하는 게 아니라 처음 for문에서 한 번더 Hit가 발생하게 된다.

- 근데, 너무 많이 데이터베이스에 Hit를 치게 되면 —> 데이터베이스에 부하가 많이 걸리게 된다. 그래서 이렇게 lazy-loading 를 방지하는 방법으로 django에서는 select_related 메소드를 사용할 수 있다.

```python
...
recommendations = Recommendation.objects.filter(visible=True).order_by('sort').select_related('restaurant').all()[:4]
...
```

- **이런식으로 views.py에서 Recommendation를 읽어올 때 select_related 메소드를 사용하면, lazy-loading를 쓰지 않고 아예 연결된 restaurant도 같이 가져오겠다는 의미가 된다.** 
  - 이 때 ‘restaurant’은 related_name이라고 생각하면 되서 modes.py에도 같은 이름으로 되어있는지 확인하자.

- [관련 내용](https://han.gl/WYHSi)
- [관련 블로그](https://velog.io/@anjaekk/Django-Django-ORM%EC%9D%98-Lazy-loading%EA%B3%BC-N1-Query-%EB%AC%B8%EC%A0%9C)


* * *

## django signal이란
- **django에는 어떤 DB 이벤트나 아니면 django에서 정의하는 여러가지 시스템 이벤트라고 할 만한 것들이 있는데, 그래서 이런 이벤트가 발생했을 때 -> 우리가 중간에 캐치를 해서 그 이벤트가 발생한 시점에 '뭔가 처리를 하고 싶다' 라는 코드를 작성해두면, django가 그걸 기억해두었다가 그 이벤트가 발생하는 전이나 후에 처리를 해주는 것을 signal이라고 한다.**



* * *

## render와 redirect 관련
- https://milooy.wordpress.com/2016/03/03/pass-data-through-redirect-in-django/


* * * 

## DRF란
- **Django REST Framework는 Django를 기반으로 REST API 서버를 만들기 위한 라이브러리이다.**
  - DRF는 어디까지나 Django를 기반으로 한 라이브러리이기 때문에 전반적으로 Django의 개발 흐름을 따라가게 된다.
- API는 웹뿐만 아니라 앱과 같은 다양한 플랫폼의 백엔드 서비스를 위해 JSON과 같은 규격화된 데이터를 제공한다. 따라서 DRF를 사용하면 기존에는 자체적인 웹 템플릿에게 바로 데이터를 전달해주는 Django 프로젝트에 국한되었던 것을 -> JSON과 같은 양식으로 다양한 플랫폼의 클라이언트에게 데이터를 제공해줄 수 있는 API 서버를 만들 수 있게 된다. 

- [DRF관련 내용](https://github.com/tkdqor/TIL/blob/main/Django/DRF(Django%20Rest%20Framework)%20%EC%A0%95%EB%A6%AC.md)

* * *

## djangorestframework-simplejwt란
- https://ganzicoder.tistory.com/34



