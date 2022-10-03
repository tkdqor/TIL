# Django

📖 **Contents**

- Django
  - [django란](#django란)
  - [django의 장점과 단점](#django의-장점과-단점)
  - [ORM의 장점과 단점](#orm의-장점과-단점)
  - [QuerySet이란](#queryset이란)
  - [values 메서드](#values-메서드)
  - [annotate와 aggregate 메서드](#annotate와-aggregate-메서드)
  - [Lazy-loading이란](#lazy-loading이란)
  - [select_related 와 prefetch_related](#select_related-와-prefetch_related)
  - [django signal이란](#django-signal이란)
  - [render와 redirect 관련](#render와-redirect-관련)
  - [DRF란](#drf란)
  - [DRF를 사용하는 이유](#drf를-사용하는-이유)
  - [DRF의 장단점](#drf의-장단점)
  - [DRF에서 APIView와 generic View의 차이점](#drf에서-apiview와-generic-view의-차이점)
  - [djangorestframework-simplejwt란](#djangorestframework-simplejwt란)
  - [swagger](#swagger)
  - [User 모델 커스텀하기](#user-모델-커스텀하기)
  - [path 파라미터와 쿼리 파라미터](#path-파라미터와-쿼리-파라미터)
  - [객체지향 프로그래밍 방식의 5원칙과 4개 특성](#객체지향-프로그래밍-방식의-5원칙과-4개-특성)
  - [django ORM distinct 메서드](#django-orm-distinct-메서드)
  - [CORS란](#cors란)
  - [Django Crontab이란](#django-crontab이란)

* * *

## django란
- django는 파이썬으로 만들어진 무료 오픈소스 웹 애플리케이션 프레임워크(web application framework)이다. 쉽고 빠르게 웹사이트를 개발할 수 있도록 돕는 구성요소로 이루어진 웹 프레임워크라고 볼 수 있다. 웹 프로그램을 만든다고 가정한다면 로그인 처리, 로그아웃 처리 및 데이터베이스 처리 등 많은 기능들을 구현해야 하는데, 웹 프레임워크는 그러한 기능들이 준비되어 있어 웹개발 및 웹사이트를 만드는데 편리하게 사용할 수 있다.

- **django를 선택한 이유** : Python 기반의 웹프레임워크이기 때문에 파이썬에서 가능한 모든 동작이 가능하다는 점이 선택하는데 큰 이유가 되었다.

* * *

## django의 장점과 단점
- **장점**
  - 이미 구현된 기능들이 많아 개발시간이 단축된다.
  - 기본적으로 어드민 페이지 기능도 구현되어 있어 등록된 모델의 기본적인 조회, 추가, 수정, 삭제 기능을 사용할 수 있고 사용자 관리도 가능하다.
  - 데이터베이스 테이블을 models.py에 클래스로 작성하고 ORM 이라는 기술을 통해 쿼리문을 몰라도 데이터베이스에 데이터를 생성, 수정 및 삭제가 가능하다.
  - django를 이용해서 django template language와 함께 html 파일을 구성하여 프론트엔드 구현이 가능해 풀스텍 경험을 할 수 있다.
  - django에서는 url에 namespace 기능이 있어 앱 내부 urls.py에 app_name과 url에 name 변수를 지정해주면, appname:pathname 형태로 url이 변경되어도 계속 연결을 해줄 수 있다는 장점이 있다.

- **단점**
  - django의 단점은 파이썬이 가지는 단점을 그대로 가져오게 된다. 인터프리터 언어이기 때문에 기능에만 초첨을 맞추면 속도가 느려질 수 있고, 실행될때까지 에러가 걸리지 않을 수 있다. 그래서 테스트 코드가 중요해진다.
  - 또한 이미 만들어진 기능을 사용하는 경우가 많기 때문에 원하는대로 커스텀을 하기가 어려울 수 있다.

* * *

## ORM의 장점과 단점
- **ORM이란, Object Relational Mapping의 약자로 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑(연결)해주는 역할을 한다.**

- **장점** : 
  - 데이터베이스를 관리할 수 있는 SQL문을 모르더라도 ORM을 이용해서 편리하게 python 코드로 SQL문으로 변환해서 DB의 데이터를 관리할 수 있다는 장점이 있다. 
  - ORM을 통해 작성한 객체를 재활용할 수 있다는 측면에서 재사용 및 유지보수의 편리성이 증가한다.
- **단점** : 
  - ORM를 하나의 프레임워크라고 생각해본다면, ORM의 형식이 정해져 있다보니 복잡한 요청을 해야하는 경우에는 오히려 ORM이 비효율적일 수 있다. 그리고 객체랑 데이터베이스를 연결한다는 개념이 ORM이기 때문에 불필요한 쿼리가 발생할 수 있다.
  - 프로젝트의 복잡성이 커질수록 난이도도 올라가고 부족한 설계로 잘못 구현되었을 경우 속도 저하 및 일관성을 무너뜨리는 문제점이 되기도 한다.

- **lazy-loading** : lazy-loading의 경우는 느리다는 단점이 될 수 있지만, 최소한의 행동으로 데이터를 가져올 수 있다는 점에서 django ORM의 장점으로 볼 수도 있다.
  - ORM에서 pk를 int로 하든 string으로 하든 조회가 되는것으로 봐서 int와 str 정도는 변환을 해주는 것 같다.

* * *

## QuerySet이란
- **QuerySet이란, 데이터베이스에서 전달 받은 모델의 객체 목록을 의미한다.**
- 데이터베이스로부터 데이터를 읽고, 필터를 걸거나 정렬을 할 수 있다.
- 리스트와 구조는 같지만 파이썬 기본 자료구조가 아니기 때문에 파이썬에서 읽고 쓰기 위해서는 자료형 변환(Casting)을 해줘야 한다.


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

```python
from django.db.models import Count

if sort == "like":
    posts = (
                Post.objects.all()
                .annotate(like=Count("likes__user_like"))
                .filter(is_deleted=False)
                .order_by("-like", "-id")
            )
```
- **위와 같이 Post 모델에 like라는 필드를 추가해서 1개의 post 당 좋아요 개수 순서대로 정렬할 수 있다. Count라는 숫자를 세는 메서드도 사용했다.**
  - Count("likes__user_like") : 여기서 likes는 Post 모델의 M:N관계 설정 필드명이고 user_like는 Post 모델의 M:N관계 설정 필드명의 related_name이다.


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
  - **Query문을 실행하는 시점**
    - Iteration
    - Slicing
    - Pickling/Caching
    - repr()
    - len()
    - list()
    - bool()
    - [공식 문서](https://docs.djangoproject.com/en/3.2/ref/models/querysets/#when-querysets-are-evaluated)


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

<br>

- **lazy-loading의 장단점**
  - 장점 : 직접적으로 데이터가 요청될 때 쿼리문을 실행하므로, 필요로 하는 경우에만 실행되어 리소스 비용이 낮아질 수 있다.
  - 단점 : N+1 Query 문제가 발생하여 비효율적으로 쿼리 횟수가 증가해서 속도가 느려지고 성능에 문제가 되는 경우가 존재한다.


- [관련 내용](https://han.gl/WYHSi)
- [관련 블로그](https://velog.io/@anjaekk/Django-Django-ORM%EC%9D%98-Lazy-loading%EA%B3%BC-N1-Query-%EB%AC%B8%EC%A0%9C)


* * *

## select_related 와 prefetch_related
- lazy-loading과 반대로 **Eager-loading(즉시 로딩)은 Lazy-loading의 반대되는 개념이다. Lazy-loading은 Query문을 하나, 하나 실행하여 데이터를 가져온다면 Eager-loading은 지금 당장 사용하지 않을 데이터도 포함하여 Query문을 실행**하기 때문에 Lazy-loading의 N+1문제의 해결책으로 많이 사용하게 된다. Django에서 Eager-loading을 실행하는 방법은 select_related 메소드와 prefetch_related 메소드를 사용한다.

- **select_related**
  - **보통 1:1관계 또는 1:N관계의 N쪽에서 사용한다.**

```python
restaurant = Restaurant.objects.get(id=1)
owner = restaurant.owner
city = owner.city
```

- City와 Owner 모델이 1:N관계이고, Owner와 Restaurant 모델도 1:N관계일 때, 위와 같이 코드를 작성하면 총 3번의 쿼리가 발생한다.

```python
restaurant = Restaurant.objects.select_related('owner__city').get(id=1)
owner = restaurant.owner
city = owner.city
```

- 반면에 위와 같이 select_related 메소드를 사용하면 1개의 쿼리만 발생한다.
- **즉, select_related 메소드는 관련된 객체(related objects) 데이터들을 가져와서 cache에 저장하게 되고 cache에 저장된 데이터를 사용하기 때문에 query를 다시 날릴 필요가 없어진다.**

<br>

- **prefetch_related**
  - **보통 M:N관계 또는 1:N관계의 1쪽에서 사용한다.**

```python
restaurants = Restaurant.objects.all()
for restaurant in restaurants:
    for pizza in restaurant.pizzas.all():
        print(restaurant.name+": "+pizza.name)
```

- Restaurant과 Pizza 모델이 서로 M:N 혹은 1:N관계일 때, 위와 같이 코드를 작성하면 총 6개의 쿼리가 발생한다.(Restaurant 모델에 5개의 데이터가 있다고 가정)

```python
restaurants = Restaurant.objects.all().prefetch_related('pizzas')
for restaurant in restaurants:
    for pizza in restaurant.pizzas.all():
        print(restaurant.name+": "+pizza.name)
```

- 반면에 위와 같이 prefetch_related 메소드를 사용하면 2개의 쿼리만 발생하게 된다.
- **즉, prefetch_related 메소드는 restaurant를 모두 가져오는 query는 동일하지만 그 뒤 pizza데이터를 가져와 result_cache에 caching하게 되고 데이터베이스에 접근하지 않고도 cache에서 찾아 사용하게 된다.**

- [관련 블로그](https://velog.io/@anjaekk/Django-Query%EC%A4%84%EC%9D%B4%EA%B8%B0selectrelated-%EC%99%80-prefetchrelated)

<br>

- **select_related와 prefetch_related 차이점**
  - Select는 DB에서 join 기능을 수행한 후 가져오므로 1 Query가 실행된다면 / prefetch는 python에서 join 기능을 수행하므로 불러올때 1 Query를 실행하고 불러온 후 1 Query를 한번 더 실행한다는 차이점이 있다.
  - [관련 블로그](https://codermun-log.tistory.com/179)

* * *

## django signal이란
- **django에는 어떤 DB 이벤트나 아니면 django에서 정의하는 여러가지 시스템 이벤트라고 할 만한 것들이 있는데, 그래서 이런 이벤트가 발생했을 때 -> 우리가 중간에 캐치를 해서 그 이벤트가 발생한 시점에 '뭔가 처리를 하고 싶다' 라는 코드를 작성해두면, django가 그걸 기억해두었다가 그 이벤트가 발생하는 전이나 후에 처리를 해주는 것을 signal이라고 한다.**

- **post_save signal 활용 예시**

```python
from django.db.models.signals import post_save
from django.dispatch import receiver

...

@receiver(post_save, sender=User)
def on_save_user(sender, instance, **kwargs):
    """
    Assignee : 상백

    django Signal을 이용하여 User 모델 생성 시, is_master 필드값이 True라면 Master 모델 객체 생성
    """

    master = Master.objects.filter(user=instance).first()
    if master is None and instance.is_master == True:
        username = instance.username
        email = instance.email
        Master.objects.create(user=instance, username=username, email=email)
```

- **from django.db.models.signals import post_save 이렇게 import 진행**
  - django db에 있는 정의된 시그널 중 하나인 post_save라는 걸 import
  - signals 패키지를 command로 들어가보면, 시그널이 여러개가 있다는 것을 확인할 수 있음
    - 들어가보면, ModelSignal이라는 이름의 클래스가 정의되어있고 pre_init, post_init, pre_save, post_save, pre_delete, post_delete와 같은 시그널들이 있다.
    - pre_init, post_init은 DB가 세팅되기전과 된 후를 의미
    - pre_save, post_save는 특정 데이터가 저장되기 직전과 직후를 의미
    - pre_delete, post_delete는 특정 데이터가 삭제되기 직전과 직후를 의미

- **from django.dispatch import receiver 진행**
  - 이건 우리가 receiver 데코레이터 패턴을 사용할 수 있기 위해 import 하는 것
  - @receiver(post_save, sender=User) 이렇게 하면 'User 모델에서 post_save가 발생하면' 이라는 의미가 되고 실제로 발생하면 이 함수가 실행된다는 의미
  - receiver 데코레이터를 사용해서 post_save를 사용할 것이고 보내는 쪽은 User 모델이라고 설정한 것이다. 즉, post_save라는 signal를 받아 그 다음 함수로 진행할 로직을 구성하는 것이다. 

- **def on_save_user와 같이 @receiver 밑에 선언하는 함수를 receiver function이라고 부른다.**
  - 이 receiver function이 필수로 받는 인자는 첫번째로 sender, 두번째로 **kwargs이다.
  - sender는 신호를 보내고자 하는 주체의 클래스를 넣으면 되고 kwargs는 signal 종류마다 다르다.
  - post_save의 경우에는 instance라는 키워드 인자를 가지는데, 이는 현재 저장된 객체, 여기서는 User 객체를 의미한다.

- **개선할 점**
  - 만약 Receiver 함수가 많아진다면, App 하위에 별도로 signals.py라는 이름으로 관리하는 것이 좋다.

- [참고 블로그](https://dgkim5360.tistory.com/entry/django-signal-example)
- [공식 문서](https://docs.djangoproject.com/en/4.0/ref/signals/)


* * *

## render와 redirect 관련
- https://milooy.wordpress.com/2016/03/03/pass-data-through-redirect-in-django/


* * * 

## DRF란
- **Django REST Framework는 Django를 기반으로 REST API 서버를 만들기 위한 라이브러리이다.**
  - DRF는 어디까지나 Django를 기반으로 한 라이브러리이기 때문에 전반적으로 Django의 개발 흐름을 따라가게 된다.

- **DRF 흐름** : URL로 클라이언트가 API 요청을 하게 되면, View에서 해당 요청을 받고 어떤 데이터들을 직렬화할지 로직을 구성한다. -> 그리고 serializer 파일에서 DB에 저장된 데이터들을 가공해서 JSON 데이터로 변환해준다. -> 그렇게 가공된 데이터들을 다시 View로 보내주게 되고 -> View에서 요청에 대한 최종 Response를 하게 된다.

- API는 웹뿐만 아니라 앱과 같은 다양한 플랫폼의 백엔드 서비스를 위해 JSON과 같은 규격화된 데이터를 제공한다. 따라서 DRF를 사용하면 기존에는 자체적인 웹 템플릿에게 바로 데이터를 전달해주는 Django 프로젝트에 국한되었던 것을 -> JSON과 같은 양식으로 다양한 플랫폼의 클라이언트에게 데이터를 제공해줄 수 있는 API 서버를 만들 수 있게 된다. 

- [DRF관련 내용](https://github.com/tkdqor/TIL/blob/main/Django/DRF(Django%20Rest%20Framework)%20%EC%A0%95%EB%A6%AC.md)
  - APIView와 ModelSerializer에 대한 내용 포함

* * *

## DRF를 사용하는 이유
- flask와 같은 프레임워크로도 api를 반환할 수 있다고 한다. 그럼에도 불구하고 DRF를 사용하는 이유를 알아보자.
  - django 기반의 **웹브라우저 API 서버**를 만들수 있도록 해준다.
  - **serializer**로 매우 편리하게 DB 즉, model에서 꺼낸 데이터를 Queryset의 형태가 아니라 **json 형태로 데이터를 전환하는 직렬화**를 할 수 있다. / 이렇게 함으로써 python으로 만든 백엔드를 다른 프론트엔드에서도 사용할 수 있게 된다.
  - 여러가지 중요한 기능들이 DRF로 이미 구현이 되어있어 사용하기 편리하다. **데이터의 유효성을 검사하는 validation이나 security**와 같은 기능들이 DRF에는 구현되어 있지만, flask는 단순히 http request를 패싱해주는 기능만 한다. 여기에 validation이나 security와 같은 기능들을 개발자가 따로 개발해야 한다면 부담이 늘어나게 될 것이다.
  - rest api를 개발 하는데 있어서, rest api 규약을 지킬수 있도록 **HTTP method 별로 로직을 구성**할 수 있게 제공해준다. / 그래서 API 에러를 더 쉽게 디버깅 할 수 있다.
  - 이미 구현된 **인증/인가 기능**을 사용하거나, DRF simple-jwt와 함께 사용함으로서 JWT 기반 인증 및 인가 기능을 유용하게 사용할 수 있다.

- [관련 블로그](https://valuefactory.tistory.com/741)

<br>

## DRF의 장단점
- **장점**
  - API 지원
  - 다양한 authentication 지원
  - class 기반의 구현 방식을 지원
  - 다양한 return 타입 제공 ex) json, csv, excel
  - custom을 통해 확장 가능
  - [관련 블로그](https://velog.io/@nameunzz/DRF)

- **단점**
  - DRF의 단점은 REST API의 단점과 유사하다고 판단했다.
  - 메소드의 한계 / REST는 HTTP 메소드를 이용하여 URI를 표현한다. 이러한 표현은 쉬운 사용이 가능하다는 장점이 있지만 반대로 메소드 형태가 제한적인 단점이 있다.
  - 표준이 없음 / REST는 설계 가이드 일 뿐이지 표준이 아니다. 명확한 표준이 없다.
  - DRF의 Serializer가 느리다는 의견이 있다. 그래서 django-ninja라는 프레임워크에 대한 얘기가 나오고 있는 중이다.
  - [관련 블로그](https://khs9628.github.io/2020/10/22/201023-DRF/)

<br>

## DRF에서 APIView와 generic View의 차이점
- **APIView**와 같이 저수준일수록 작성하는 코드가 많고 중복, 반복을 포함하고 있지만 상대적으로 로직이 이해하기 쉽다.
- 반면에 **Generic View**와 같이 고수준으로 갈수록 코드가 짧아지고 중복, 반복 코드가 거의 없지만 동작 코드가 추상화되어 있다.
  - **DRF Mixins이나 Generic View, Viewset까지 전부 APIView와 다르게 클래스 레벨에서 queryset과 serializer_class를 설정해서 사용한다는 점이 다르다.**

<br>

- **프로젝트에서 왜 APIView를 선택했는지 물어본다면**
  - DRF를 원티드 프리온보딩 코스에서 처음으로 사용해봤기 때문에 로직을 이해하기 쉬운 APIView로 비록 중복되는 코드들이 있었으나 get, post 등 메소드마다 함수로 로직을 구현해보려고 했다. 나중에는 학습을 해서 Generic View를 사용하여 반복되는 코드를 줄이고 생산성을 높여보고 싶다.


* * *

## djangorestframework-simplejwt란
- **DRF로 API를 구현하는 과정에서 JWT(Json Web Token) 기반 인증을 구현하기 위해서는, django의 JWT 패키지를 설치해줘야 한다.**
  - django의 JWT 패키지에는 **djangorestframework-jwt와 djangorestframework-simplejwt**가 있지만, 전자는 더이상 업데이트가 진행되지 않기 때문에 djangorestframework-simplejwt를 설치해서 사용하면 된다.

- **이러한 djangorestframework-simplejwt를 설치해서 DRF기반의 API 서버 구축 시, JWT 기반 인증을 구현할 수 있다.**
  - 클라이언트가 아이디와 패스워드를 통해 웹서비스에 인증이 되면, 서버에서 JWT를 생성하여 클라이언트에게 access token, refresh token을 응답으로 돌려준다.
  - 그 다음, 클라이언트가 HTTP 헤더에 JWT인 access token을 첨부해서 서버에 데이터를 요구하게 되고, 서버에서는 클라이언트로부터 온 JWT 검증해서 응답해준다.
  - access token이 만료되면 refresh token을 통해 새로운 access token을 요청할 수 있다.

- **JWT token의 경우, header에 관련 내용 넣고 payload에 유저 id 넣고 마지막으로 django 서버의 secret key를 넣어서 알고리즘을 통해 암호화를 진행한다.**
  - 이렇게 토큰을 만들면, 이 secret key로만 복호화가 가능하기 때문에 django 서버에서만 복호화를 할 수 있다.
  - 우리한테 토큰이 날라오면 Auth : baerer “...” 이러한 형태가 된다. 그러면 서버는 data = JWT.detoken(‘sdfsdf’) 이런식으로 복호화를 진행한다.
  - JWT토큰은 결과적으로 복호화를 하면 header와 payload로 이루어져 있는 JSON 데이터가 나오게 된다.

- [DRF simpleJWT를 사용해서 진행한 로그인 구현 코드 예시](https://github.com/tkdqor/TIL/blob/main/Django/DRF(Django%20Rest%20Framework)%20%EC%A0%95%EB%A6%AC.md#%ED%9A%8C%EC%9B%90%EA%B0%80%EC%9E%85%EA%B3%BC-%EB%A1%9C%EA%B7%B8%EC%9D%B8-apiview-%EC%98%88%EC%8B%9C)

- **DRF simpleJWT를 사용하기 위한 settings 설정**
  - DRF simpleJWT를 사용하기 위해 settings.py에 SIMPLE_JWT라는 값들을 정의해준다. 간략하게 각각 어떤 의미인지 알아보자
  - ROTATE_REFRESH_TOKENS : True로 설정 시, access token이 만료되서 TokenRefreshView에 다시 요청할 때, refresh token도 같이 새롭게 갱신해준다
  - BLACKLIST_AFTER_ROTATION : True로 설정 시, 블랙리스트 앱이 사용 중이고 ROTATE_REFRESH_TOKENS 설정이 True로 설정된 경우 TokenRefreshView에 제출된 새로 고침 토큰이 블랙리스트에 추가된다.
  - UPDATE_LAST_LOGIN : True로 설정하면 로그인 시 auth_user 테이블의 last_login 필드가 업데이트된다
  - ALGORITHM : 토큰에 대한 서명/확인 작업을 수행하는 데 사용되는 PyJWT 라이브러리의 알고리즘이다. 대칭 HMAC 서명 및 확인을 사용하려면 'HS256', 'HS384', 'HS512' 알고리즘을 사용할 수 있고 HMAC 알고리즘을 선택하면 SIGNING_KEY 설정이 서명 키와 확인 키로 사용되고 이 경우 VERIFYING_KEY 설정은 무시된다.
  - SIGNING_KEY : 생성된 토큰의 콘텐츠에 서명하는 데 사용되는 서명 키이다. HMAC 서명의 경우 서명 프로토콜에 필요한 데이터 비트 수 이상을 포함하는 임의 문자열이어야 한다. RSA 서명의 경우 2048비트 이상의 RSA 개인 키가 포함된 문자열이어야 한다. Simple JWT는 기본적으로 256비트 HMAC 서명을 사용하므로 SIGNING_KEY 설정은 기본적으로 django 프로젝트의 SECRET_KEY 설정 값으로 설정된다.
  - VERIFYING_KEY : 생성된 토큰의 내용을 확인하는 데 사용되는 확인 키이다. 
  - AUDIENCE : 청중은 생성된 토큰에 포함되거나 디코딩된 토큰에서 유효성이 확인된다고 주장한다. 없음으로 설정하면 이 필드가 토큰에서 제외되고 유효성이 검사되지 않는다.
  - ISSUER : 발행자는 생성된 토큰에 포함되고/또는 디코딩된 토큰에서 검증된다고 주장한다. 없음으로 설정하면 이 필드가 토큰에서 제외되고 유효성이 검사되지 않는다.
  - JWK_URL : JWK_URL은 토큰 서명을 확인하는 데 필요한 공개 키를 동적으로 확인하는 데 사용한다.
  - LEEWAY : Leeway는 만료 시간에 약간의 여유를 주기 위해 사용된다. 초 또는 datetime.timedelta의 정수일 수 있다.
  - AUTH_HEADER_TYPES : 인증이 필요한 보기에 대해 승인될 인증 헤더 유형이다. 예를 들어, 'Bearer' 값은 인증이 필요한 보기가 다음 형식의 헤더를 검색함을 의미한다. Authorization: Bearer <token>
  - AUTH_HEADER_NAME : 인증에 사용할 인증 헤더이름이다. 본값은 요청의 Authorization 헤더를 수락하는 HTTP_AUTHORIZATION이다.
  - USER_ID_FIELD : 사용자를 식별하기 위해 생성된 토큰에 포함될 사용자 모델의 데이터베이스 필드이다. 이 설정의 값은 초기 값이 선택되면 일반적으로 변경되지 않는 필드를 지정하는 것이 좋다.
  - USER_ID_CLAIM : 사용자 식별자를 저장하는 데 사용되는 생성된 토큰의 클레임이다. 예를 들어 'user_id' 설정 값은 생성된 토큰에 사용자의 식별자가 포함된 "user_id" 클레임이 포함됨을 의미한다.
  - USER_AUTHENTICATION_RULE : 사용자가 인증할 수 있는지 여부를 결정하기 위해 호출 가능하다.
  - AUTH_TOKEN_CLASSES : 인증을 증명할 수 있는 토큰 유형을 지정하는 클래스에 대한 점 경로 목록이다.
  - TOKEN_TYPE_CLAIM : 토큰 유형을 저장하는 데 사용되는 클레임 이름이다.
  - TOKEN_USER_CLASS : 검증된 토큰으로 뒷받침되는 상태 비저장 사용자 객체
  - JTI_CLAIM : 토큰의 고유 식별자를 저장하는 데 사용되는 클레임 이름
  - SLIDING_TOKEN_REFRESH_EXP_CLAIM : 슬라이딩 토큰의 새로 고침 기간 만료 시간을 저장하는 데 사용되는 클레임 이름
  - SLIDING_TOKEN_LIFETIME : 슬라이딩 토큰이 인증을 증명하는 데 유효한 기간을 지정하는 datetime.timedelta 개체
  - SLIDING_TOKEN_REFRESH_LIFETIME : 슬라이딩 토큰이 새로 고침되는 데 유효한 기간을 지정하는 datetime.timedelta 개체
  - [공식 문서](https://django-rest-framework-simplejwt.readthedocs.io/en/latest/settings.html#access-token-lifetime)


- [관련 블로그](https://medium.com/chanjongs-programming-diary/django-rest-framework-drf-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-jwt-%EA%B8%B0%EB%B0%98-authentication-%EC%84%B8%ED%8C%85%ED%95%98%EA%B8%B0-with-simplejwt-%EC%B4%88%EA%B8%B0-%ED%99%98%EA%B2%BD-%EC%84%B8%ED%8C%85-1-e54c3ed2420c)
- [관련 블로그2](https://velog.io/@kjyeon1101/JWT-%EC%9D%B8%EC%A6%9D%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%9C-%ED%9A%8C%EC%9B%90%EA%B0%80%EC%9E%85%EB%A1%9C%EA%B7%B8%EC%9D%B8)


* * *

## swagger
- **drf-yasg란, drf-yasg는 장고로 정의된 API를 문서화할 수 있는 패키지로, (django rest framework- Yet another Swagger generator)의 약자**
- **swagger는 프로젝트 내 많은 API를 한꺼번에 문서화하여 볼 수 있게 하는 패키지로, API 관리에 매우 용이**

```terminal
pip install drf-yasg 또는 
pipenv install drf-yasg 명령어로 설치
```

- **settings.py의 INSTALLED_APPS에 drf_yasg 추가하기**
```python
INSTALLED_APPS = [
    'drf_yasg', #drf_yasg
]
```

- **그리고 urls.py 설정**
```python
...
# swagger 화면 Info 설정
schema_view_v1 = get_schema_view(
    openapi.Info(
        title="PAYHERE API",
        default_version="v1",
        description="PAYHERE API",
        terms_of_service="https://www.google.com/policies/terms/",
    ),
    public=True,
    permission_classes=(AllowAny,),
)

# swagger 관련 url
urlpatterns += [
    re_path(r"^swagger(?P<format>\.json|\.yaml)$", schema_view_v1.without_ui(cache_timeout=0), name="schema-json"),
    re_path(r"^swagger/$", schema_view_v1.with_ui("swagger", cache_timeout=0), name="schema-swagger-ui"),
    re_path(r"^redoc/$", schema_view_v1.with_ui("redoc", cache_timeout=0), name="schema-redoc"),
]
```

* * *

## User 모델 커스텀하기
  
### User 모델을 커스텀하는 이유
- 원래 django에는 기본적으로 정의되어있는 User 모델이 있는데, 그 모델에서는 로그인할 때 username으로 로그인을 하게 되어있다. 그래서 다른 필드로 로그인을 하고 싶거나, 기본적으로 정의되어 있는 User 모델보다 다양한 정보를 정의하고 싶을 수 있다. 이렇게 정해져있는 필드가 아닌 다양하게 User 모델을 사용하기 위해서는 커스텀 User 모델을 적용하는 것이 좋다.
  
<br>  
  
### User 모델 커스텀 진행 과정
- **커스텀 유저 모델을 사용하기 위해서는, models.py에서 BaseUserManager와 AbstractBaseUser 2개의 클래스를 상속받아 새로운 클래스를 생성해야 한다.**

- **BaseUserManager 클래스 : User를 생성할때 사용하는 클래스**
  - 해당 클래스의 소스코드에는, email 부분을 @ 기준으로 email_name과 domain_part로 나누고 이 둘을 합친 email을 return 해준다. 그리고, 랜덤으로 password를 만들어준다.
  - User를 생성할 때 사용하는 헬퍼 클래스이다. User를 생성할 때의 행위를 지정할 수 있다.
    - 모든 django model들은 Manager를 통해서 QuerySet을 받는다. DB에서 query를 처리할 때,  Manager를 무조건 거쳐야 한다고 한다.
  - BaseUserManager 클래스 내부에는 create_user() 함수와 create_superuser() 함수가 있다. 각각 일반 유저를 생성하는 함수, 관리자 유저를 생성하는 함수이다.
    - create_user 메서드는 email를 필수 입력 필드로 지정할 수 있고, 최종적으로 user 객체를 생성할 수 있게 해주는 메서드이다.
    - create_superuser 메서드는 admin 계정을 생성할 수 있게 해주는 메서드이다.

<br>  
  
- **AbstractBaseUser 클래스 : 상속받아 User 모델을 생성하는 클래스**
  - 해당 클래스의 소스코드에는, password를 CharField로 저장하고 save 메서드를 오버라이딩 해서 Model 클래스의 save 메서드를 super()로 실행해서 저장한다. 그리고 get_username이라는 메서드로는 생성된 user의 username를 리턴한다. clean이라는 메서드로는 유니코드 문자열 username를 정규화 형식으로 반환한다. 
  - 소스코드에 있는 [@property 관련 내용](https://github.com/tkdqor/TIL/blob/main/%EA%B8%B0%EC%88%A0%20%EB%A9%B4%EC%A0%91%20%EB%8C%80%EB%B9%84%20%EA%B8%B0%EB%B3%B8%20%EA%B0%9C%EB%85%90%20%EC%A0%95%EB%A6%AC/Python.md#property-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
    - @property 설정이 된 is_anonymous 메서드로는 항상 False가 리턴된다. User 객체가 익명이 아니기 때문에 항상 False가 리턴된다.
    - @property 설정이 된 is_authenticated 메서드로는 항상 True가 리턴된다. User 객체가 인증이 되었는지 여부를 알려준다.
    - check_password 메서드로 password가 적합한지 체크해준다.
    - get_session_auth_hash 메서드는 암호 필드의 HMAC를 반환한다. / key_salt에서 생성된 키와 비밀(기본값은 settings.SECRET_KEY)을 사용하여 '값'의 HMAC를 반환한다. 기본 알고리즘은 SHA1이지만 hashlib.new()에서 지원하는 모든 알고리즘 이름을 전달할 수 있다.
    - @classmethod 설정이 된 get_email_field_name 메서드는 email 필드를 가져온다.
    - @classmethod 설정이 된 normalize_username 메서드는 유니코드 문자열 username를 정규화 형식으로 반환한다.
  - User 모델의 필드를 설정해준다. id필드를 primary_key로 설정해서 인덱스를 설정 / 다른 추가적인 필드를 설정할 수 있다.
  - USERNAME_FIELD 설정값으로 email를 설정하면 로그인 시 ID를 email로 설정할 수 있다.
  - 그리고 objects 변수값을 통해 헬퍼 클래스를 지정해야한다. 위에서 BaseUserManager를 상속해서 설정한 클래스를 설정해주면 된다.
  - has_perm 메서드로 로그인 사용자의 특정 테이블 CRUD 권한 설정을 할 수 있다.
  - has_module_perms 메서드로 로그인 사용자의 특정 app에 접근 가능 여부를 설정할 수 있다.

<br>  
  
- **models.py 예시**

```python
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager
from django.db import models

"""Create your models here."""


class CustomUserManager(BaseUserManager):
    """
    custom user model 사용 시 UserManager 클래스와
    create_user, create_superuser 함수가 정의되어 있어야 함
    """

    def create_user(self, email, password):
        if not email:
            raise ValueError("Users must have an email")
        user = self.model(
            email=email,
        )
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, email, password):
        """python manage.py createsuperuser 사용 시 해당 함수가 사용됨"""
        user = self.create_user(
            email=email,
            password=password,
        )
        user.is_admin = True
        user.save(using=self._db)
        return user


class User(AbstractBaseUser):
    id = models.BigAutoField(primary_key=True)
    email = models.EmailField("이메일 주소", max_length=100, unique=True)
    password = models.CharField("비밀번호", max_length=128)
    username = models.CharField("이름", max_length=20)
    mobile = models.CharField("휴대폰 번호", max_length=20)

    """is_active가 False일 경우 계정이 비활성화됨"""
    is_active = models.BooleanField("활성화", default=True)

    """is_staff에서 해당 값 사용"""
    is_admin = models.BooleanField("관리자", default=False)

    created_at = models.DateTimeField("작성시간", auto_now_add=True)
    updated_at = models.DateTimeField("수정시간", auto_now=True)

    """"
    # id로 사용 할 필드 지정.
    # 로그인 시 USERNAME_FIELD에 설정 된 필드와 password가 사용된다.
    """
    USERNAME_FIELD = "email"

    """user를 생성할 때 입력받을 필드 지정"""
    REQUIRED_FIELDS = []

    """custom user 생성 시 필요"""
    objects = CustomUserManager()

    def __str__(self):
        return f"email: {self.email} / name: {self.username}"

    def has_perm(self, perm, obj=None):
        """
        # 로그인 사용자의 특정 테이블의 crud 권한을 설정, perm table의 crud 권한이 들어간다.
        # admin일 경우 항상 True, 비활성 사용자(is_active=False)의 경우 항상 False
        """
        return True

    def has_module_perms(self, app_label):
        """ "
        # 로그인 사용자의 특정 app에 접근 가능 여부를 설정, app_label에는 app 이름이 들어간다.
        # admin일 경우 항상 True, 비활성 사용자(is_active=False)의 경우 항상 False
        """
        return True

    @property
    def is_staff(self):
        """admin 권한 설정"""
        return self.is_admin
```

- **CustomUserManager의 경우에는,** 일반 유저를 생성할 때 create_user 함수가 호출되고 email과 password를 받게끔 설정한 예시이다. user.set_password(password)로 암호화를 하고, user.save(using=self._db) 코드로 DB에 저장한다.
  - 관리자 유저를 생성할 때는 create_superuser 함수가 호출되고 user.is_admin = True 이렇게 어드민 여부를 True로 설정해준다.


- **User 모델의 경우, AbstractBaseUser를 상속받아서 정의한다.**
  - id = models.BigAutoField(primary_key=True) 이런식으로 User 모델의 primary_key를 id 필드로 정해준다.
  - is_active 필드로 계정 활성화 여부를 변경할 수 있다. 
  - is_admin 필드로 관리자 계정으로 변경할 수 있다.
  - USERNAME_FIELD = "email" => 이렇게 설정하면, 로그인 시 email 정보를 받게된다.
  - custom user 생성 시, objects = CustomUserManager() 이러한 코드를 설정해준다.

- **그리고 마지막으로 settings.py에 아래와 같은 코드를 추가한다. user model이 AbstractUser를 상속할 때는 반드시 settings.py 에서 AUTH_USER_MODEL = '폴더명.클래스명' 값을 추가해야 migrate가 제대로 진행된다.**
```python
AUTH_USER_MODEL = "accounts.User"
```

- [참고 블로그](https://hckcksrl.medium.com/django-%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%9C%A0%EC%A0%80-%EB%AA%A8%EB%8D%B8-custom-user-model-b8487c0d150)

* * *

## path 파라미터와 쿼리 파라미터

- **path 파라미터** : 엔드포인트에서 변수가 되는 부분을 <> 꺽쇠로 표현해서 사용하는 파라미터. 
- **쿼리 파라미터** : 엔드포인트에서 Optional하게 사용되는 것으로 url 마지막에 ? 다음으로 쿼리 파라미터의 이름을 작성하고 = 다음에 값을 입력해서 사용하게 된다.

* * *

## 객체지향 프로그래밍 방식의 5원칙과 4개 특성
- **객체지향 프로그래밍 방식(Object-Oriented Programming)이란?**
  - 큰 문제를 작게 쪼개는 것이 아니라, 작은 문제들을 해결하는 객체를 만들어서 객체들을 조합해 큰 문제를 해결하는 Bottom-Up 방식의 프로그래밍을 의미

- **OOP의 장점과 단점**
  - 장점
    - 코드의 재사용성이 높아진다. 유지보수가 쉽다. 코드가 간결해진다.
  - 단점
    - 처리 시간이 비교적 오래 걸린다. 프로그램을 설계할 때 많은 고민과 시간을 투자해야한다.

- **OOP의 5원칙(SOLID)**
  - **SRP(Single Responsibility Principle)**
    - 단일 책임 원칙으로 객체 즉, 하나의 클래스는 오직 하나의 책임만을 가져야 한다는 의미이다. 특정 목적과 관련된 클래스는 그 목적과 관련해서만 수정이 이루어져야 한다. 이렇게 클래스의 목적을 명확하게 정해서 기능을 분리하자는 것이다.
  - **OCP(Open/Closed Principle)**
    - 개방-폐쇄 원칙으로 객체는 확장에는 열려(Open) 있으나, 변경에는 닫혀(Closed)있어야 한다는 의미이다. 즉, 객체 기능의 확장을 허용하고 스스로의 변경은 피해야 한다.
  - **LSP(Liskov’s Substitution Principle)**
    - 리스코프 치환 원칙으로 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다는 의미이다. 즉, 자식 클래스는 언제나 자신의 부모 클래스를 대체할 수 있어야 하고, 부모 클래스가 들어갈 자리에 자식 클래스를 넣어도 계획대로 잘 작동해야 한다는 것이다.
  - **ISP(Interface Segregation Principle)**
    - 인터페이스 분리 원칙으로 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다는 의미이다. 그래서 클라이언트에서 사용하지 않는 메서드가 없게끔 인터페이스를 작게 나누어야 한다는 것이다.
  - **DIP(Dependency Inversion Principle)**
    - 의존성 역전 원칙으로 추상성이 높고 안정적인 고수준의 클래스는 구체적이고 불안정한 저수준의 클래스에 의존해서는 안 된다는 의미이다. 저수준 클래스가 고수준 클래스에서 정의한 추상 타입에 의존해야 한다. 객체 지향 프로그래밍에서는 객체들 사이에 메세지를 주고 받기 위해 의존성이 생기는데, 의존성 역전의 원칙은 올바른 의존 관계를 위한 원칙에 해당된다. 변경이 없는 추상화된 클래스를 고수준이라고 하고, 변하기 쉬운 구체 클래스를 저수준이라고 한다.

- **OOP의 4가지 특징**
  - **캡슐화** : 객체가 정의된 이후, 객체에 필요한 데이터나 기능(메소드)을 하나로 묶어 책임이 있는 객체에 그룹화시켜주는 것. 또한, 코드를 안전하게 보호하고 접근하지 않게끔 캡슐화를 진행한다.
  - **상속** : 자식 클래스가 부모 클래스의 특성과 기능을 물려받는 것. 기능의 일부분을 변경하는 경우 자식 클래스에서 상속받아 수정 및 사용할 수 있도록 한다. 클래스의 재사용이 용이하도록 해준다. 
    - **ex) python에서는 부모 클래스를 상속하면 상속받은 부모 클래스의 \_\_init\__ 특성과 모든 메서드를 자식 클래스가 그대로 사용 가능하다.**
    - 또한, 자식 클래스가 부모 클래스의 메서드를 오버라이딩하면, 오버라이딩한 메서드가 먼저 실행된다.
  - **추상화** : 클래스들의 공통적인 특성(변수, 메소드)들을 묶어 표현하는 것을 의미. 즉, 객체들의 공통적인 속성과 기능을 중심으로 추상화를 하게 되서 기능을 구현하는 것을 의미한다.
  - **다형성** : 어떤 변수,메소드가 상황에 따라 다른 결과를 내는 것을 의미. ex) 오버로딩(Overloading) : 하나의 클래스에서 메소드의 이름이 같지만, 파라메터가 다른 것 / 오버라이딩(Overriding) : 부모 클래스의 메소드를 자식 클래스의 용도에 맞게 재정의하여 코드의 재사용성을 높임

- [참고 블로그](https://velog.io/@ygh7687/OOP%EC%9D%98-5%EC%9B%90%EC%B9%99%EA%B3%BC-4%EA%B0%80%EC%A7%80-%ED%8A%B9%EC%84%B1)
- [참고 블로그2](https://youngjinmo.github.io/2021/04/features-of-oop/)

* * * 

## django ORM distinct 메서드
- distinct 메서드는 쿼리셋 결과에서 중복 레코드를 제거할 수 있게 해준다.
- distinct()에서 특정 필드를 조건으로 지정하고 싶은 경우 values_list()와 조합할 수 있다.
```python
# Person 모델의 name 필드를 distinct 조건으로 설정해서 중복 제거
Person.objects.distinct().values_list('name')
```

-distinct()와 order_by()를 조합할 때는, order_by()에 지정한 필드가 DISTINCT에 추가된다.
```python
# Person 모델의 name 필드를 기준으로 정렬하고 distinct 조건으로 설정해서 중복 제거
Person.objects.distinct().order_by('name')
```

* * *

## CORS란
- CORS란, **Cross Origin Resource Sharing**의 약자로 도메인 또는 포트가 다른 서버의 자원을 요청하는 매커니즘을 의미한다.
- 즉, 다른 도메인을 가진 서버의 URL을 호출해 데이터를 가져오려고 하는 경우 보안 문제인 Cross Domain 이슈를 발생시킨다.
  - 이렇게 이슈를 발생시키는 이유는, 외부 서버가 요청한 데이터를 함부로 유출시킬 수 없기 때문에 보안적인 문제로 통제를 하고 있는 것이다.
  - ex) 백엔드는 django의 DRF로 개발하고 프론트는 리액트로 개발하는 경우, django는 기본적으로 8000번 포트를 사용하고, 리액트에서는 3000번 포트를 사용하기 때문에 두 프레임워크의 포트가 달라서 문제가 발생할 수 있다.

- **CORS 문제 해결 방법**
  - 먼저 pip install로 django-cors-headers를 설치해준다. django-cors-headers는 CORS에 필요한 서버의 헤더를 조작하기 위한 Django 앱이다. 
  - 이렇게 설치하게 되면 외부 서버에서 보내는 요청의 헤더에 접근을 허락하는 내용이 추가된다.
  - settings.py의 installed_apps와 MIDDLEWARE에 관련 내용을 추가해주고 CORS_ORIGIN_WHITELIST에 cross-site 요청을 허용하는 호스트들을 입력해주면 된다.

- [관련 블로그](https://oen-blog.tistory.com/46)

* * *

## Django Crontab이란
- 크론탭이란, 시간을 정해서 특정 프로그램을 자동으로 동작하게 하는 기능인데 django Crontab을 이용해서 장고에서 크론탭을 활용할 수 있다. 이걸 설치해서 django 파일 내에서 설정할 수 있도록 모듈을 제공하는 것이다.
- [관련 블로그](https://moondol-ai.tistory.com/220), [관련 블로그2](https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%B0%98%EB%B3%B5_%EC%98%88%EC%95%BD%EC%9E%91%EC%97%85_cron,_crond,_crontab)

