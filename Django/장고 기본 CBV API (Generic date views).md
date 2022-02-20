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
- 지정 날짜필드 역순으로 정렬된 목록 à 최신 목록을 보고자 할 때 사용
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
