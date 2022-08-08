# Django

📖 **Contents**

- Django
  - [django의 장점](#django의-장점)
  - [ORM의 장점과 단점](#orm의-장점과-단점)
  - [QuerySet이란](#queryset이란)
  - [values 메서드](#values-메서드)
  - [annotate와 aggregate 메서드](#annotate와-aggregate-메서드)
  - [Lazy-loading이란](#lazy-loading이란)
  - [select_related 와 prefetch_related](#select_related-와-prefetch_related)
  - [django signal이란](#django-signal이란)
  - [render와 redirect 관련](#render와-redirect-관련)
  - [DRF란](#drf란)
  - [djangorestframework-simplejwt란](#djangorestframework-simplejwt란)
  - [swagger](#swagger)
  - [User 모델 커스텀하기](#user-모델-커스텀하기)
  - [path 파라미터와 쿼리 파라미터](#path-파라미터와-쿼리-파라미터)

* * *

## django의 장점
- 이미 구현된 기능들이 많아 개발시간이 단축된다.
- 기본적으로 제공되는 어드민 페이지를 이용해서 개발을 수월하게 진행할 수 있다.
- ORM이 있어 쿼리를 알지 못해도 데이터베이스에 데이터를 생성, 수정 및 삭제가 가능하다.
- 모델링도 쉽게 구현이 가능하다.
- django를 이용해서 프론트엔드 구현이 가능해 풀스텍 경험을 할 수 있다.

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

* * *

## djangorestframework-simplejwt란
- https://ganzicoder.tistory.com/34


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
- **커스텀 유저 모델을 사용하기 위해서는, models.py에서 BaseUserManager와 AbstractBaseUser 2개의 클래스를 상속받아 새로운 클래스를 생성해야 한다.**
  - **BaseUserManager 클래스 : User를 생성할때 사용하는 클래스**
    - BaseUserManager 클래스 내부에는 create_user() 함수와 create_superuser() 함수가 있다. 각각 일반 유저를 생성하는 함수, 관리자 유저를 생성하는 함수이다.
  - **AbstractBaseUser 클래스 : 상속받아 User 모델을 생성하는 클래스**

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


- [관련 블로그](https://hckcksrl.medium.com/django-%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%9C%A0%EC%A0%80-%EB%AA%A8%EB%8D%B8-custom-user-model-b8487c0d150)

* * *

## path 파라미터와 쿼리 파라미터

- **path 파라미터** : 엔드포인트에서 변수가 되는 부분을 <> 꺽쇠로 표현해서 사용하는 파라미터. 
- **쿼리 파라미터** : 엔드포인트에서 Optional하게 사용되는 것으로 url 마지막에 ? 다음으로 쿼리 파라미터의 이름을 작성하고 = 다음에 값을 입력해서 사용하게 된다.



