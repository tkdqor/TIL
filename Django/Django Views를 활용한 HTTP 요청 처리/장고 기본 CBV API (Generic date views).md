## Built-in CBV API 중, Generic date views
- 날짜 관련된 필터링을 하는 클래스 / https://docs.djangoproject.com/en/3.2/ref/class-based-views/generic-date-based/ 
- ArchiveIndexView : 지정 날짜필드 역순으로 정렬된 목록
- YearArchiveView : 지정 year년도의 목록
- MonthArchiveView : 지정 year/month 월의 목록
- WeekArchiveView : 지정 year/week 주의 목록
- DayArchiveView : 지정 year/month/day 일의 목록
- TodayArchiveView : 오늘 날짜의 목록
- DateDetailView : 지정 year/month/day 목록 중에서 특정 pk의 detail
  - DetailView와 비교 : URL에 year/month/day를 쓰고자 할 경우에 유용 
  - DetailView는 지정 pk 혹은 지정 slug의 레코드를 찾아서 데이터를 보는 것인데 DateDetailView의 경우에는 지정 pk 혹은 slug의 detail 화면을 꾸미는 건 똑같지만 거기에 추가 조건으로 url로 year, month, day가 추가가 된다.

- 각각의 클래스 기반 뷰는 각각이 요청하는 이름의 인자를 url pattern에서 넘겨줘야 정상적으로 작동이 된다.
  - 이러한 클래스 기반 뷰의 공통옵션으로, allow_future (디폴트: False) 가 있다. 미래 시간을 허용하겠느냐를 묻는 것이다. False일 경우에는 현재 시간 이후의 records는 queryset에서 제외가 된다. 미래시간을 허용하려면 True로 바꿔주자.


### ArchiveIndexView
- 지정 날짜필드 역순으로 정렬된 목록 -> 최신 목록을 보고자 할 때 사용
- 필요한 URL 인자는 없다.
- 필수 옵션은 model / date_field : 정렬 기준 필드 / date_list_period (디폴트: "year")

```python
from django.views.generic import ArchiveIndexView
from .models import Post

post_archive = ArchiveIndexView.as_view(
model=Post,	date_field='created_at')
```

- 그래서 코드처럼 어떤 모델에 대한 리스트를 보여줄 것이며 이 때 어떤 날짜필드에 대해서 역순정렬을 할 것인지 설정하면 된다.
- 그리고 해당 queryset에 대해 date_list를 (날짜목록) 제공해준다. 그래서 이 View는 지금 date_list_period의 디폴트가 year이니까 date_list에 year단위로 그룹핑을 해서 year 리스트를 제공해준다. 만약, month로 수정하면 월단위로 해준다.
- 디폴트 template_name_suffix : "_archive.html"
- Context로는 다음과 같은 사항을 전달해준다.(즉, html에서 django template language 변수로 latest와 date_list가 활용가능하다) -> latest : QuerySet / date_list : 등록된 Record의 년도 목록    


```python
urlpatterns = [
    path('archive/', views.post_archive, name='post_archive'),
]
```

- urls.py에서 설정하고

```python
post_archive = ArchiveIndexView.as_view(model=Post, date_field='created_at')
```

- views.py에서도 설정한 다음, 앱 내부 templates - 앱이름 디렉터리 안에 post_archive.html을 새롭게 생성해주자.

```html
<h2>latest</h2>

{{ latest }}

<h2>date_list</h2>

{{ date_list }}
```

- 이렇게 설정하면 해당 필드 역순으로 latest가 전체를 보여준다. 그리고 date_list는 전체 queryset이 모두 2022년에 생성했으니 2022년으로 표시가 된다. 다른 날짜를 확인해보기 위해 작업을 해보자.

```terminal
In [1]: from instagram.models import Post

In [2]: post_list = Post.objects.all()

In [3]: import random

In [4]: for post in post_list:
   ...:     year = random.choice(range(1990, 2022))
   ...:     month = random.choice(range(1, 13))
   ...:     post.created_at = post.created_at.replace(year=year, month=month)
   ...:     post.save()
   
   
In [6]: Post.objects.all().values_list("created_at")
Out[6]: <QuerySet [(datetime.datetime(2018, 2, 17, 2, 15, 3, 45194, tzinfo=<UTC>),), (datetime.datetime(2000, 11, 17, 2, 15, 3, 44857, tzinfo=<UTC>),), (datetime.datetime(1991, 8, 17, 2, 15, 3, 44511, tzinfo=<UTC>),), (datetime.datetime(2003, 4, 17, 2, 15, 3, 44140, tzinfo=<UTC>),), (datetime.datetime(2003, 8, 17, 2, 15, 3, 43801, tzinfo=<UTC>),), (datetime.datetime(1993, 7, 17, 2, 15, 3, 43483, tzinfo=<UTC>),), (datetime.datetime(1999, 5, 17, 2, 15, 3, 43163, tzinfo=<UTC>),), (datetime.datetime(1994, 10, 17, 2, 15, 3, 42831, tzinfo=<UTC>),), (datetime.datetime(2008, 5, 17, 2, 15, 3, 42483, tzinfo=<UTC>),), (datetime.datetime(1993, 9, 17, 2, 15, 3, 42144, tzinfo=<UTC>),), (datetime.datetime(2006, 8, 17, 2, 15, 3, 41816, tzinfo=<UTC>),), (datetime.datetime(2000, 3, 17, 2, 15, 3, 41463, tzinfo=<UTC>),), (datetime.datetime(1994, 12, 17, 2, 15, 3, 41073, tzinfo=<UTC>),), (datetime.datetime(2019, 2, 17, 2, 15, 3, 40758, tzinfo=<UTC>),), (datetime.datetime(2000, 5, 17, 2, 15, 3, 40426, tzinfo=<UTC>),), (datetime.datetime(2014, 7, 17, 2, 15, 3, 40104, tzinfo=<UTC>),), (datetime.datetime(2007, 12, 17, 2, 15, 3, 39773, tzinfo=<UTC>),), (datetime.datetime(2013, 8, 17, 2, 15, 3, 39440, tzinfo=<UTC>),), (datetime.datetime(1993, 2, 17, 2, 15, 3, 39118, tzinfo=<UTC>),), (datetime.datetime(2002, 7, 17, 2, 15, 3, 38794, tzinfo=<UTC>),), '...(remaining elements truncated)...']>   
   

In [7]: Post.objects.all().values_list("created_at__year")
Out[7]: <QuerySet [(2018,), (2000,), (1991,), (2003,), (2003,), (1993,), (1999,), (1994,), (2008,), (1993,), (2006,), (2000,), (1994,), (2019,), (2000,), (2014,), (2007,), (2013,), (1993,), (2002,), '...(remaining elements truncated)...']>


In [8]: Post.objects.all().values_list("created_at__year", flat=True)
Out[8]: <QuerySet [2018, 2000, 1991, 2003, 2003, 1993, 1999, 1994, 2008, 1993, 2006, 2000, 1994, 2019, 2000, 2014, 2007, 2013, 1993, 2002, '...(remaining elements truncated)...']>


In [9]: year_list = _


In [11]: set(year_list)
Out[11]: 
{1990,
 1991,
 1992,
 1993,
 1994,
 1995,
 1996,
 1997,
 1998,
 1999,
 2000,
 2001,
 2002,
 2003,
 2004,
 2005,
 2006,
 2007,
 2008,
 2009,
 2010,
 2011,
 2012,
 2013,
 2014,
 2015,
 2016,
 2017,
 2018,
 2019,
 2020,
 2021}
```

- replace()는 python의 DateTimeField에서 지원해준다. 그래서 특정 년, 월을 변경하고자 할 때 가능하다.
- Post 모델의 created_at 필드는 데이터가 생성될 때 값이 자동으로 들어가게끔 설정했기에 이미 생성되어있는 대상을 가져와서 하는 것이라 모델에서 직접 바뀌는 건 아니다. 그냥 임의로 변경을 해보는 것이다.
- **Post.objects.all().values_list("created_at")** -> 이렇게 입력하면 지정한 필드만 값을 가져올 수 있게 된다.
  - 또한, **Post.objects.all().values_list("created_at__year")** -> 이런식으로 필드 뒤에 언더바 2개로 year를 입력하면 년도만 그 필드의 연도만 가져올 수 있다. 여기까지는 개별로 리스트 퀘리셋에 튜플이 들어있는데 flat 옵션을 주면 튜플에서 빠져나오게 된다.
- 그리고 django shell에서 직전 결과는 _로 불러올 수 있다. year_list = _ -> 이건 year_list라는 변수에 직전 결과값을 담은 것이다.
- 또한, set()를 활용해서 year_list 변수에 다양한 년도가 있는 것을 확인할 수 있다.

- 위에 작업을 진행한 다음, 다시 웹 브라우저 화면을 보면 다양한 년도가 나오게 된다.

```python
post_archive = ArchiveIndexView.as_view(model=Post, date_field='created_at', paginate_by=10)
```

- views.py에서 paginate_by=10 이렇게 설정하면 화면에 10개 단위로 queryset이 출력된다.

```html
<h2>latest</h2>

{{ latest }}

<h2>date_list</h2>

{% for date in date_list %}
    {{ date }}
    {{ date.year }}
    {{ date|date:"Y" }}
{% endfor %}
```

- html에서 date_list를 하나씩 출력해보면 다른 정보들이 나오지만 디폴트인 year만 의미있는 데이터로 나오게 된다. 대신 {{ date.year }}로 출력하면 년도만 출력된다.
- 혹은 원하는 문자열로 datetime 객체에서 지원해주는 function이 있다. 파이프 객체가 있다. 파이프는 django template 문법에서는 필터라는 함수를 의미한다. jango template 안에서 파이프를 쓰고 입력하면 된다. 그래서 첫번째 인자로 date라는 값이 출력되고 두번째 인자로 "Y" 이렇게 넘어가게 된다.(무슨말인지 이해 필요..)
  - 내가 이해한 것은, date가 하나 출력될 때 그 값이 Year만 나오게끔 필터링 해준 것이다..

- 이 포맷은 django template tag 공식 문서에 보면 이와 관련된 포메팅 포맷이 있다.


### YearArchiveView
- 지정 year년도의 목록
- 필요한 URL 인자 : "year" 
- 옵션
  - model, date_field / date_list_period (디폴트: "month") - 지정 년도에서 month 단위로 Record가 있는 날짜 리스트 / make_object_list (디폴트: False) - 거짓일 경우, object_list를 비움. 그래서 특정 연도를 보고 그 연도에 해당하는 object_list를 보고자 한다면 make_object_list를 True로 변경하면 된다.

- Context
  - year, previous_year, next_year / date_list: 전체 Record의 월 목록 / object_list

- 디폴트 template_name_suffix : "_archive_year.html"

```python
# urls.py 설정
urlpatterns	=	[
re_path(r'^archive/(?P<year>\d{4})/$',	...),
]

# views.py 설정
from django.views.generic.dates	import YearArchiveView
from .models import Post

class PostYearArchiveView(YearArchiveView):
  model	=	Post
  date_field = 'created_at'
  #	make_object_list = False
```

- 예시로 직접 해본다면,

```python
post_archive_year = YearArchiveView.as_view(model=Post, date_field='created_at')
```

- views.py에서 다음과 같이 설정하고 

```python
urlpatterns = [
    path('archive/<year:year>/', views.post_archive_year, name='post_yarchive_year'),
]
```

- 위와 같이 urls.py에서는 year라는 인자를 꼭 넣어줘야 한다. 그리고 template에 post_archive_year.html를 만들고

```html
<h2>year</h2>
{{ year }}

<h2>previous_year</h2>
{{ previous_year }}

<h2>next_year</h2>
{{ next_year }}

<h2>date_list</h2>
{{ date_list }}

<h2>object_list</h2>
{{ object_list }}
```

- 제공되는 context를 작성해보자. 그러면 출력이 된다. {{ date_list }}라고 하면 이제 년과 월까지 의미있는 데이터가 된다. {{ object_list }}는 현재 비어있다. 그런데, views.py에서 
  - post_archive_year = YearArchiveView.as_view(model=Post, date_field='created_at', make_object_list=True) 이렇게 make_object_list=True 로 주면 object_list가 채워지게 된다.


<img width="1134" alt="image" src="https://user-images.githubusercontent.com/95380638/154829831-ab49ea2f-098b-4a09-988b-0a8b330247ab.png">
- 이렇게 백엔드가 준비되어있고 프론트엔드를 꾸밀 수 있다면 좋을 것이다.

* * *
19분부터 converter 설명 다시보기..!


### MonthArchiveView
- 지정 year/month 월의 목록
- 필요한 URL 인자 : "year", "month"
- 옵션
  - month_format (디폴트: "%b") / 숫자 포맷은 "%m"
- 디폴트 template_name_suffix : "_archive_month.html"
- Context
  - month, previous_month, next_month / date_list: 전체 Record의 날짜 목록 / object_list


```python
# urls.py
urlpatterns	=	[
re_path(r'^archive/(?P<year>\d{4})/(?P<month>\d{1,2})/$',	...),
]

# views.py
from django.views.generic.dates	import MonthArchiveView
from .models	import Post

class PostMonthArchiveView(MonthArchiveView):
  model	=	Post
  date_field	=	'created_at'
  month_format	=	'%m'
```  


### WeekArchiveView
- 지정 year/week 주의 목록
- 필요한 URL 인자 : "year", "week"
- 옵션
  - week_format / "%U" (디폴트) : 한 주의 시작을 일요일로 지정 / "%W" : 한 주의 시작을 월요일로 지정

- 디폴트 template_name_suffix : "_archive_week.html"
- Context
  - week, previous_week, next_week / date_list: 전체 Record의 날짜 목록 / object_list

```python
# urls.py
urlpatterns	=	[
    re_path(r'^archive/(?P<year>\d{4})/week/(?P<week>\d{1,2})/$',	...),
]

# views.py
from django.views.generic.dates	import WeekArchiveView
from .models	import Post

class PostWeekArchiveView(WeekArchiveView):
  model	=	Post
  date_field	=	'created_at'
  week_format	=	'%U'
```


### DayArchiveView
- 지정 year/month/day 일의 목록
- 필요한 URL 인자 : "year", "month", "day"
- 옵션 : month_format (디폴트: "%b") / 숫자 포맷은 "%m"
- 디폴트 template_name_suffix : "_archive_day.html"
- Context
  - day, previous_day, next_day / date_list: 전체 Record의 날짜 목록 / object_list

```python
# urls.py
urlpatterns	=	[
    re_path(r'^archive/(?P<year>\d{4})/'
    r'(?P<month>\d{1,2})/(?P<day>\d{1,2})/$',	...),
]

# views.py
from django.views.generic.dates	import DayArchiveView
from .models	import Post

class PostDayArchiveView(DayArchiveView):
    model	=	Post
    date_field	=	'created_at'
    month_format	=	'%m'
```


### TodayArchiveView
- 오늘 날짜의 목록
- 필요한 URL 인자 : 없음.
- DayArchiveView와 유사하게 동작하지만, year/month/day 인자를 받지 않는다.  /  previous_day, next_day 미제공

```python
# views.py
from django.views.generic.dates	import TodayArchiveView
from .models	import Post

post_today_archive	=	TodayArchiveView.as_view(
    model=Post,	date_field='created_at')
```


### DateDetailView
- 지정 year/month/day 목록 중에서 특정 pk의 detail
- 필요한 URL 인자 : "year", "month", "day", "pk"(or "slug")

```python
# urls.py
urlpatterns	=	[
    re_path(r'^archive/(?P<year>\d{4})/(?P<month>\d{1,2})/(?P<day>\d{1,2})/?P<pk>\d+/$',	...),
]

# views.py
from django.views.generic.dates	import DateDetailView
from .models	import Post

class PostDateDetailView(DateDetailView):
    model	=	Post
    date_field	=	'created_at'
    month_format	=	'%m'
```    

