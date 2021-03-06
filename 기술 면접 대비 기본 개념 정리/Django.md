# Django

๐ **Contents**

- Django
  - [django์ ์ฅ์ ](#django์-์ฅ์ )
  - [values ๋ฉ์๋](#values-๋ฉ์๋)
  - [annotate์ aggregate ๋ฉ์๋](#annotate์-aggregate-๋ฉ์๋)
  - [Lazy-loading์ด๋](#lazy-loading์ด๋)
  - [django signal์ด๋](#django-signal์ด๋)
  - [render์ redirect ๊ด๋ จ](#render์-redirect-๊ด๋ จ)
  - [DRF๋](#drf๋)
  - [djangorestframework-simplejwt๋](#djangorestframework-simplejwt๋)
  - [swagger](#swagger)
  - [User ๋ชจ๋ธ ์ปค์คํํ๊ธฐ](#user-๋ชจ๋ธ-์ปค์คํํ๊ธฐ)

* * *

## django์ ์ฅ์ 
- ์ด๋ฏธ ๊ตฌํ๋ ๊ธฐ๋ฅ๋ค์ด ๋ง์ ๊ฐ๋ฐ์๊ฐ์ด ๋จ์ถ๋๋ค.
- ๊ธฐ๋ณธ์ ์ผ๋ก ์ ๊ณต๋๋ ์ด๋๋ฏผ ํ์ด์ง๋ฅผ ์ด์ฉํด์ ๊ฐ๋ฐ์ ์์ํ๊ฒ ์งํํ  ์ ์๋ค.
- ORM์ด ์์ด ์ฟผ๋ฆฌ๋ฅผ ์์ง ๋ชปํด๋ ๋ฐ์ดํฐ๋ฒ ์ด์ค์ ๋ฐ์ดํฐ๋ฅผ ์์ฑ, ์์  ๋ฐ ์ญ์ ๊ฐ ๊ฐ๋ฅํ๋ค.
- ๋ชจ๋ธ๋ง๋ ์ฝ๊ฒ ๊ตฌํ์ด ๊ฐ๋ฅํ๋ค.
- django๋ฅผ ์ด์ฉํด์ ํ๋ก ํธ์๋ ๊ตฌํ์ด ๊ฐ๋ฅํด ํ์คํ ๊ฒฝํ์ ํ  ์ ์๋ค.


* * *

## values ๋ฉ์๋
- **values** : iterable๋ก ์ฌ์ฉ๋  ๋ ๋ชจ๋ธ ์ธ์คํด์ค๊ฐ ์๋ ์ฌ์ ์ ๋ฐํํ๋ QuerySet๋ฅผ ๋ฐํ
  - [๊ณต์ ๋ฌธ์](https://docs.djangoproject.com/en/4.0/ref/models/querysets/#django.db.models.query.QuerySet.values)
```python
# This list contains a Blog object.
>>> Blog.objects.filter(name__startswith='Beatles')
<QuerySet [<Blog: Beatles Blog>]>

# This list contains a dictionary.
>>> Blog.objects.filter(name__startswith='Beatles').values()
<QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>
```

- **values() ๋ฉ์๋์ ํน์  ํ๋๋ฅผ ์ง์ ํ๋ฉด ๊ฐ ์ฌ์ ์๋ ์ง์ ํ ํ๋์ ๋ํ ํ๋ ํค/๊ฐ๋ง ํฌํจ๋๋ค.**
```python
>>> Blog.objects.values()
<QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>
>>> Blog.objects.values('id', 'name')
<QuerySet [{'id': 1, 'name': 'Beatles Blog'}]>
```

<br>

## annotate์ aggregate ๋ฉ์๋

- **annotate** : ์์์ column๋ฅผ ํ๋ ์ถ๊ฐํ๋ ๊ฒ๊ณผ ๊ฐ์ด ํ๋ ํ๋๋ฅผ ๋ง๋ค๊ณ  ๊ฑฐ๊ธฐ์ ๋ค๋ฅธ ํ๋์ ๊ฐ์ ๊ทธ๋๋ก ๋ณต์ฌํ๊ฑฐ๋, ๋ค๋ฅธ ํ๋์ ๊ฐ๋ค์ ์กฐํฉํ ๊ฐ์ ๋ฃ์ ์ ์๊ฒ ํด์ฃผ๋ ๋ฉ์๋์ด๋ค.
  - annotate์ ์ฌ์ ์  ์๋ฏธ๋ '์ฃผ์์ ๋ฌ๋ค'๋ผ๋ ๊ฒ์ผ๋ก django์์๋ ์ฃผ์ ๋์  ํ๋๋ฅผ ์ถ๊ฐํ๋ค๊ณ  ์๊ฐํ๋ฉด ๋๋ค.

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
- **์์ ๊ฐ์ด Post ๋ชจ๋ธ์ like๋ผ๋ ํ๋๋ฅผ ์ถ๊ฐํด์ 1๊ฐ์ post ๋น ์ข์์ ๊ฐ์ ์์๋๋ก ์ ๋ ฌํ  ์ ์๋ค. Count๋ผ๋ ์ซ์๋ฅผ ์ธ๋ ๋ฉ์๋๋ ์ฌ์ฉํ๋ค.**
  - Count("likes__user_like") : ์ฌ๊ธฐ์ likes๋ Post ๋ชจ๋ธ์ M:N๊ด๊ณ ์ค์  ํ๋๋ช์ด๊ณ  user_like๋ Post ๋ชจ๋ธ์ M:N๊ด๊ณ ์ค์  ํ๋๋ช์ related_name์ด๋ค.


<br>

- **aggregate** : django์์ ํ ํ๋ ์ ์ฒด์ ํฉ์ด๋ ํ๊ท , ๊ฐ์ ๋ฑ์ ๊ณ์ฐํ  ๋ ์ฌ์ฉํ  ์ ์๋ ๋ฉ์๋์ด๋ค.
  - aggregate์ ์ฌ์ ์  ์๋ฏธ๋ 'ํฉ๊ณ', '์ขํฉ'์ด๋ค.
  - **์๋์ ๊ฐ์ด ๋์๋๋ฆฌ ํํ๋ก ์ถ๋ ฅํด์ค๋ค.**

```python
# OrderLog ๋ชจ๋ธ์ price๋ผ๋ ํ๋์ ๋ชจ๋  ๊ฐ์ ๋ํด์ฃผ๊ธฐ
>>> OrderLog.objects.all().aggregate(total_price=Sum('price'))
{'total_price': 262200}
```

- [๊ด๋ จ ๋ธ๋ก๊ทธ](http://raccoonyy.github.io/django-annotate-and-aggregate-like-as-excel/)
- [๊ด๋ จ ๋ธ๋ก๊ทธ2](https://tech.toktokhan.dev/2021/04/26/django-query-func/)


* * *

## Lazy-loading์ด๋
- Django์ ORM์ ๋ค๋ฅธ ORM๊ณผ ๋ง์ฐฌ๊ฐ์ง๋ก Lazy-loading๋ฐฉ์์ ์ฌ์ฉํ๋ค. **Lazy-loading์ด๋ python ์ฝ๋๋ก ORM์์ ๋ช๋ น์ ์คํํ  ๋๋ง๋ค ๋ฐ์ดํฐ๋ฒ ์ด์ค์ ์ ๊ทผํ์ฌ ๋ฐ์ดํฐ๋ฅผ ๊ฐ์ ธ์ค๋ ๊ฒ์ด ์๋ ๋ชจ๋  ๋ช๋ น์ฒ๋ฆฌ๊ฐ ๋๋๊ณ  ์ค์  ๋ฐ์ดํฐ๋ฅผ ๋ถ๋ฌ์์ผํ  ๋ ๋ฐ์ดํฐ๋ฒ ์ด์ค Query๋ฌธ์ ์คํํ๋ ๋ฐฉ์์ ๋งํ๋ค.**
  - ๋ฐ๋๋ก **Eager-loading(์ฆ์ ๋ก๋ฉ)์ย Lazy-loading์ ๋ฐ๋ ๊ฐ๋์ด๋ค.ย Lazy-loading์ย Query๋ฌธ์ ํ๋, ํ๋ ์คํํ์ฌ ๋ฐ์ดํฐ๋ฅผ ๊ฐ์ ธ์จ๋ค๋ฉด Eager-loading์ย ์ง๊ธ ๋น์ฅ ์ฌ์ฉํ์ง ์์ ๋ฐ์ดํฐ๋ ํฌํจํ์ฌย Query๋ฌธ์ ์คํ**ํ๊ธฐ ๋๋ฌธ์ ๋ฐ์ ์ค๋ชํ ย Lazy-loading์ย N+1๋ฌธ์ ์ ํด๊ฒฐ์ฑ์ผ๋ก ๋ง์ด ์ฌ์ฉํ๊ฒ ๋๋ค. Django์์ย Eager-loading์ ์คํํ๋ ๋ฐฉ๋ฒ์ย select_relatedย ๋ฉ์๋์ย prefetch_relatedย ๋ฉ์๋๋ฅผ ์ฌ์ฉํ๋ค.ย 

- ์ฐ๋ฆฌ๊ฐ ORM์ผ๋ก ์ฟผ๋ฆฌ๋ฌธ์ ๋ง๋ค๋ฉด ๋ฐ๋ก ๋ฐ์ดํฐ๋ฒ ์ด์ค์ Hit๊ฐ ๋๋ ๊ฒ์ด ์๋๋ผ, template์์ {% for item in recommendations %}ย  ๋๋ {{ item.restaurant.category.name }} ์ด๋ฌํ ์ฝ๋๊ฐ ์คํ๋  ๋ ๋ง๋ค ๋ฐ์ดํฐ๋ฒ ์ด์ค์ Hit๋ฅผ ์น๊ฒ ๋๋ค. ์ด๊ฒ ๋ฐ๋ก โlazy-loading ๋ฐฉ์โ์ด๋ค. ๊ทธ ๋ค์ ๋ฐ๋ก ์ด์ด์ {{ item.restaurant.main_image.image }} ํด๋น ์ฝ๋๊ฐ ์คํ๋  ๋๋, ์ด๋ฏธ ๋ฉ๋ชจ๋ฆฌ์ ๋ถ๋ฌ์จ restaurant์ด ์๊ธฐ ๋๋ฌธ์ Hit ํ์ง๋ ์๋๋ค. 
  - **Lazy-loading์ ์ฑ๋ฅ์ด์์ธ N+1 Query ๋ฌธ์ ๋ ์ธ๋ํค(Foreign Key)๋ฅผ ์ฐธ์กฐํด์ ๋ฐ์ดํฐ๋ฅผ ๊ฐ์ ธ์ฌ ๋ ๋ฐ์ํ๋ค.** {% for item in recommendations %} ์ด๋ฌํ ์ฝ๋๊ฐ ์๊ณ  ๊ทธ ๋ฐ์ {{ item.restaurant.category.name }} ์ด๋ ๊ฒ ์ฝ๋๊ฐ ์์ ๋, ๋ฐ์ดํฐ ๊ฐ์์ธ N๋ฒ Hit ํ๋ ๊ฒ ์๋๋ผ ์ฒ์ for๋ฌธ์์ ํ ๋ฒ๋ Hit๊ฐ ๋ฐ์ํ๊ฒ ๋๋ค.

- ๊ทผ๋ฐ, ๋๋ฌด ๋ง์ด ๋ฐ์ดํฐ๋ฒ ์ด์ค์ Hit๋ฅผ ์น๊ฒ ๋๋ฉด โ> ๋ฐ์ดํฐ๋ฒ ์ด์ค์ ๋ถํ๊ฐ ๋ง์ด ๊ฑธ๋ฆฌ๊ฒ ๋๋ค. ๊ทธ๋์ ์ด๋ ๊ฒ lazy-loading ๋ฅผ ๋ฐฉ์งํ๋ ๋ฐฉ๋ฒ์ผ๋ก django์์๋ select_related ๋ฉ์๋๋ฅผ ์ฌ์ฉํ  ์ ์๋ค.

```python
...
recommendations = Recommendation.objects.filter(visible=True).order_by('sort').select_related('restaurant').all()[:4]
...
```

- **์ด๋ฐ์์ผ๋ก views.py์์ Recommendation๋ฅผ ์ฝ์ด์ฌ ๋ select_related ๋ฉ์๋๋ฅผ ์ฌ์ฉํ๋ฉด, lazy-loading๋ฅผ ์ฐ์ง ์๊ณ  ์์ ์ฐ๊ฒฐ๋ restaurant๋ ๊ฐ์ด ๊ฐ์ ธ์ค๊ฒ ๋ค๋ ์๋ฏธ๊ฐ ๋๋ค.** 
  - ์ด ๋ โrestaurantโ์ related_name์ด๋ผ๊ณ  ์๊ฐํ๋ฉด ๋์ modes.py์๋ ๊ฐ์ ์ด๋ฆ์ผ๋ก ๋์ด์๋์ง ํ์ธํ์.

- [๊ด๋ จ ๋ด์ฉ](https://han.gl/WYHSi)
- [๊ด๋ จ ๋ธ๋ก๊ทธ](https://velog.io/@anjaekk/Django-Django-ORM%EC%9D%98-Lazy-loading%EA%B3%BC-N1-Query-%EB%AC%B8%EC%A0%9C)


* * *

## django signal์ด๋
- **django์๋ ์ด๋ค DB ์ด๋ฒคํธ๋ ์๋๋ฉด django์์ ์ ์ํ๋ ์ฌ๋ฌ๊ฐ์ง ์์คํ ์ด๋ฒคํธ๋ผ๊ณ  ํ  ๋งํ ๊ฒ๋ค์ด ์๋๋ฐ, ๊ทธ๋์ ์ด๋ฐ ์ด๋ฒคํธ๊ฐ ๋ฐ์ํ์ ๋ -> ์ฐ๋ฆฌ๊ฐ ์ค๊ฐ์ ์บ์น๋ฅผ ํด์ ๊ทธ ์ด๋ฒคํธ๊ฐ ๋ฐ์ํ ์์ ์ '๋ญ๊ฐ ์ฒ๋ฆฌ๋ฅผ ํ๊ณ  ์ถ๋ค' ๋ผ๋ ์ฝ๋๋ฅผ ์์ฑํด๋๋ฉด, django๊ฐ ๊ทธ๊ฑธ ๊ธฐ์ตํด๋์๋ค๊ฐ ๊ทธ ์ด๋ฒคํธ๊ฐ ๋ฐ์ํ๋ ์ ์ด๋ ํ์ ์ฒ๋ฆฌ๋ฅผ ํด์ฃผ๋ ๊ฒ์ signal์ด๋ผ๊ณ  ํ๋ค.**



* * *

## render์ redirect ๊ด๋ จ
- https://milooy.wordpress.com/2016/03/03/pass-data-through-redirect-in-django/


* * * 

## DRF๋
- **Django REST Framework๋ Django๋ฅผ ๊ธฐ๋ฐ์ผ๋ก REST API ์๋ฒ๋ฅผ ๋ง๋ค๊ธฐ ์ํ ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ด๋ค.**
  - DRF๋ ์ด๋๊น์ง๋ Django๋ฅผ ๊ธฐ๋ฐ์ผ๋ก ํ ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ด๊ธฐ ๋๋ฌธ์ ์ ๋ฐ์ ์ผ๋ก Django์ ๊ฐ๋ฐ ํ๋ฆ์ ๋ฐ๋ผ๊ฐ๊ฒ ๋๋ค.
- API๋ ์น๋ฟ๋ง ์๋๋ผ ์ฑ๊ณผ ๊ฐ์ ๋ค์ํ ํ๋ซํผ์ ๋ฐฑ์๋ ์๋น์ค๋ฅผ ์ํด JSON๊ณผ ๊ฐ์ ๊ท๊ฒฉํ๋ ๋ฐ์ดํฐ๋ฅผ ์ ๊ณตํ๋ค. ๋ฐ๋ผ์ DRF๋ฅผ ์ฌ์ฉํ๋ฉด ๊ธฐ์กด์๋ ์์ฒด์ ์ธ ์น ํํ๋ฆฟ์๊ฒ ๋ฐ๋ก ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํด์ฃผ๋ Django ํ๋ก์ ํธ์ ๊ตญํ๋์๋ ๊ฒ์ -> JSON๊ณผ ๊ฐ์ ์์์ผ๋ก ๋ค์ํ ํ๋ซํผ์ ํด๋ผ์ด์ธํธ์๊ฒ ๋ฐ์ดํฐ๋ฅผ ์ ๊ณตํด์ค ์ ์๋ API ์๋ฒ๋ฅผ ๋ง๋ค ์ ์๊ฒ ๋๋ค. 

- [DRF๊ด๋ จ ๋ด์ฉ](https://github.com/tkdqor/TIL/blob/main/Django/DRF(Django%20Rest%20Framework)%20%EC%A0%95%EB%A6%AC.md)

* * *

## djangorestframework-simplejwt๋
- https://ganzicoder.tistory.com/34


* * *

## swagger
- **drf-yasg๋, drf-yasg๋ ์ฅ๊ณ ๋ก ์ ์๋ API๋ฅผ ๋ฌธ์ํํ  ์ ์๋ ํจํค์ง๋ก, (django rest framework- Yet another Swagger generator)์ ์ฝ์**
- **swagger๋ ํ๋ก์ ํธ ๋ด ๋ง์ API๋ฅผ ํ๊บผ๋ฒ์ ๋ฌธ์ํํ์ฌ ๋ณผ ์ ์๊ฒ ํ๋ ํจํค์ง๋ก, API ๊ด๋ฆฌ์ ๋งค์ฐ ์ฉ์ด**

```terminal
pip install drf-yasg ๋๋ 
pipenv install drf-yasg ๋ช๋ น์ด๋ก ์ค์น
```

- **settings.py์ INSTALLED_APPS์ drf_yasg ์ถ๊ฐํ๊ธฐ**
```python
INSTALLED_APPS = [
    'drf_yasg', #drf_yasg
]
```

- **๊ทธ๋ฆฌ๊ณ  urls.py ์ค์ **
```python
...
# swagger ํ๋ฉด Info ์ค์ 
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

# swagger ๊ด๋ จ url
urlpatterns += [
    re_path(r"^swagger(?P<format>\.json|\.yaml)$", schema_view_v1.without_ui(cache_timeout=0), name="schema-json"),
    re_path(r"^swagger/$", schema_view_v1.with_ui("swagger", cache_timeout=0), name="schema-swagger-ui"),
    re_path(r"^redoc/$", schema_view_v1.with_ui("redoc", cache_timeout=0), name="schema-redoc"),
]
```

* * *

## User ๋ชจ๋ธ ์ปค์คํํ๊ธฐ
- **์ปค์คํ ์ ์  ๋ชจ๋ธ์ ์ฌ์ฉํ๊ธฐ ์ํด์๋, models.py์์ BaseUserManager์ AbstractBaseUser 2๊ฐ์ ํด๋์ค๋ฅผ ์์๋ฐ์ ์๋ก์ด ํด๋์ค๋ฅผ ์์ฑํด์ผ ํ๋ค.**
  - **BaseUserManager ํด๋์ค : User๋ฅผ ์์ฑํ ๋ ์ฌ์ฉํ๋ ํด๋์ค**
    - BaseUserManager ํด๋์ค ๋ด๋ถ์๋ create_user() ํจ์์ create_superuser() ํจ์๊ฐ ์๋ค. ๊ฐ๊ฐ ์ผ๋ฐ ์ ์ ๋ฅผ ์์ฑํ๋ ํจ์, ๊ด๋ฆฌ์ ์ ์ ๋ฅผ ์์ฑํ๋ ํจ์์ด๋ค.
  - **AbstractBaseUser ํด๋์ค : ์์๋ฐ์ User ๋ชจ๋ธ์ ์์ฑํ๋ ํด๋์ค**

- **models.py ์์**

```python
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager
from django.db import models

"""Create your models here."""


class CustomUserManager(BaseUserManager):
    """
    custom user model ์ฌ์ฉ ์ UserManager ํด๋์ค์
    create_user, create_superuser ํจ์๊ฐ ์ ์๋์ด ์์ด์ผ ํจ
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
        """python manage.py createsuperuser ์ฌ์ฉ ์ ํด๋น ํจ์๊ฐ ์ฌ์ฉ๋จ"""
        user = self.create_user(
            email=email,
            password=password,
        )
        user.is_admin = True
        user.save(using=self._db)
        return user


class User(AbstractBaseUser):
    id = models.BigAutoField(primary_key=True)
    email = models.EmailField("์ด๋ฉ์ผ ์ฃผ์", max_length=100, unique=True)
    password = models.CharField("๋น๋ฐ๋ฒํธ", max_length=128)
    username = models.CharField("์ด๋ฆ", max_length=20)
    mobile = models.CharField("ํด๋ํฐ ๋ฒํธ", max_length=20)

    """is_active๊ฐ False์ผ ๊ฒฝ์ฐ ๊ณ์ ์ด ๋นํ์ฑํ๋จ"""
    is_active = models.BooleanField("ํ์ฑํ", default=True)

    """is_staff์์ ํด๋น ๊ฐ ์ฌ์ฉ"""
    is_admin = models.BooleanField("๊ด๋ฆฌ์", default=False)

    created_at = models.DateTimeField("์์ฑ์๊ฐ", auto_now_add=True)
    updated_at = models.DateTimeField("์์ ์๊ฐ", auto_now=True)

    """"
    # id๋ก ์ฌ์ฉ ํ  ํ๋ ์ง์ .
    # ๋ก๊ทธ์ธ ์ USERNAME_FIELD์ ์ค์  ๋ ํ๋์ password๊ฐ ์ฌ์ฉ๋๋ค.
    """
    USERNAME_FIELD = "email"

    """user๋ฅผ ์์ฑํ  ๋ ์๋ ฅ๋ฐ์ ํ๋ ์ง์ """
    REQUIRED_FIELDS = []

    """custom user ์์ฑ ์ ํ์"""
    objects = CustomUserManager()

    def __str__(self):
        return f"email: {self.email} / name: {self.username}"

    def has_perm(self, perm, obj=None):
        """
        # ๋ก๊ทธ์ธ ์ฌ์ฉ์์ ํน์  ํ์ด๋ธ์ crud ๊ถํ์ ์ค์ , perm table์ crud ๊ถํ์ด ๋ค์ด๊ฐ๋ค.
        # admin์ผ ๊ฒฝ์ฐ ํญ์ True, ๋นํ์ฑ ์ฌ์ฉ์(is_active=False)์ ๊ฒฝ์ฐ ํญ์ False
        """
        return True

    def has_module_perms(self, app_label):
        """ "
        # ๋ก๊ทธ์ธ ์ฌ์ฉ์์ ํน์  app์ ์ ๊ทผ ๊ฐ๋ฅ ์ฌ๋ถ๋ฅผ ์ค์ , app_label์๋ app ์ด๋ฆ์ด ๋ค์ด๊ฐ๋ค.
        # admin์ผ ๊ฒฝ์ฐ ํญ์ True, ๋นํ์ฑ ์ฌ์ฉ์(is_active=False)์ ๊ฒฝ์ฐ ํญ์ False
        """
        return True

    @property
    def is_staff(self):
        """admin ๊ถํ ์ค์ """
        return self.is_admin
```

- **CustomUserManager์ ๊ฒฝ์ฐ์๋,** ์ผ๋ฐ ์ ์ ๋ฅผ ์์ฑํ  ๋ create_user ํจ์๊ฐ ํธ์ถ๋๊ณ  email๊ณผ password๋ฅผ ๋ฐ๊ฒ๋ ์ค์ ํ ์์์ด๋ค. user.set_password(password)๋ก ์ํธํ๋ฅผ ํ๊ณ , user.save(using=self._db) ์ฝ๋๋ก DB์ ์ ์ฅํ๋ค.
  - ๊ด๋ฆฌ์ ์ ์ ๋ฅผ ์์ฑํ  ๋๋ create_superuser ํจ์๊ฐ ํธ์ถ๋๊ณ  user.is_admin = True ์ด๋ ๊ฒ ์ด๋๋ฏผ ์ฌ๋ถ๋ฅผ True๋ก ์ค์ ํด์ค๋ค.


- **User ๋ชจ๋ธ์ ๊ฒฝ์ฐ, AbstractBaseUser๋ฅผ ์์๋ฐ์์ ์ ์ํ๋ค.**
  - id = models.BigAutoField(primary_key=True) ์ด๋ฐ์์ผ๋ก User ๋ชจ๋ธ์ primary_key๋ฅผ id ํ๋๋ก ์ ํด์ค๋ค.
  - is_active ํ๋๋ก ๊ณ์  ํ์ฑํ ์ฌ๋ถ๋ฅผ ๋ณ๊ฒฝํ  ์ ์๋ค. 
  - is_admin ํ๋๋ก ๊ด๋ฆฌ์ ๊ณ์ ์ผ๋ก ๋ณ๊ฒฝํ  ์ ์๋ค.
  - USERNAME_FIELD = "email" => ์ด๋ ๊ฒ ์ค์ ํ๋ฉด, ๋ก๊ทธ์ธ ์ email ์ ๋ณด๋ฅผ ๋ฐ๊ฒ๋๋ค.
  - custom user ์์ฑ ์, objects = CustomUserManager() ์ด๋ฌํ ์ฝ๋๋ฅผ ์ค์ ํด์ค๋ค.


- [๊ด๋ จ ๋ธ๋ก๊ทธ](https://hckcksrl.medium.com/django-%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%9C%A0%EC%A0%80-%EB%AA%A8%EB%8D%B8-custom-user-model-b8487c0d150)

