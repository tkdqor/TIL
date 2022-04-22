## JSON과 비동기 호출
- 이번에는 페이지네이션을 Ajax를 통해서 호출하는 방법을 알아보자.


### Ajax
- Ajax는 Asynchronous JavaScript and XML의 첫글자를 딴 것으로 자바스크립트를 사용해서 비동기적인 웹 어플리케이션의 개발을 위한 개발 기법을 일컫는 용어이다.
- 자바스크립트의 XMLHttpRequest라는 객체를 이용해서 정해진 데이터 형식으로 데이터를 주고 받는 형태
  - 이러한 Ajax는 유채화를 그릴 때 그림의 일부만 덧칠해서 다시 그리는 느낌과 비슷하다. 즉, 웹 페이지의 일부 영역만 새로운 데이터로 그릴 수 있도록 도와주는 기법이라고 생각하면 된다.

- 과거에 Ajax가 처음 나왔을 때는, 서버의 데이터를 XML이라는 형식으로 주고 받았지만 현재에 이르러서는 더 경량화된 JSON이라는 데이터로 주고 받는 게 일반적인 형태가 되었다.


### Ajax 사용 이유 (캡처 참고)
- **속도가 향상될 수 있다.** 보통 웹페이지가 로딩될 때 이미지 CSS, 자바스크립트 등의 웹 페이지를 띄우기 위한 파일들을 서버에 요청해야 하고, 페이지가 이동될 때마다 파일들을 매번 불러와야 한다.
  그런데, Ajax를 사용하면 굳이 다시 불러올 필요가 없는 데이터는 제외하고 페이지에 특정 영역에 필요한 데이터만을 필요한만큼 가져와서 로딩할 수 있다. 따라서 더 빠르게 사용자에게 정보를 불러와주는 게 
  가능하다.
- **그리고 통신 데이터가 감소한다.** 위와 동일한 이유로 필요한 데이터만 그 때 그 때 가져오기 때문에, 사용자에게 보여주는 정보량에 비해 전체 통신하는 데이터의 크기는 줄어든다.
- **마지막으로 부드러운 사용자 인터렉션이 가능하다.** 보통 페이지를 이동할 때마다 페이지 전체가 로딩되면서 흰색으로 깜빡이는 현상이 보이게 된다. 이걸 Ajax를 활용하면 DOM API를 사용해서 --> 
  전체 화면을 새로 그리지 않고 일부분만 필요할때마다 수정하기 때문에 자연스러운 사용자 경험을 제공할 수 있다. 


### Ajax를 통해서 받아오는 데이터의 형태 (캡처 참고)
- 우리가 웹 페이지에 방문하면, 웹 페이지를 그려주는 html/css/자바스크립트 등의 코드등이 데이터로 전달되지만, Ajax의 경우에는 이미 로딩된 html/css/자바스크립트 등을 다시 로딩할 필요가 없으므로 정말 필요한 데이터만 서버에서 가져오게 된다. 
- 여기서 XML과 JSON이 등장하게 된다.
  - **XML**의 경우는 마크업 기반의 언어로, html과 비슷한 형태의 언어라고 보면 된다. 즉, 마크업 언어의 확장형이다. 그래서 원래는 이런식으로 웹 서비스에서 데이터를 주고받다가 데이터가 많고 탐색 속도가 느리다는 단점이 많았다.
  - 그래서 **JSON**이라는 형태가 등장했다. JavaScript Object Notation의 약자로, 즉 Javascript의 객체 리터럴 형태를 거의 동일하게 텍스트로 사용하는 방식이다. 최대한 불필요한 형식을 줄여 데이터를 가볍게 보내기 위한 형식이다. 
  - **JSON은 같은 데이터를 표현해도 XML보다 더 간단하게 표현되기 때문에 Ajax에서는 훨씬 대중적으로 많이 사용된다.** 캡처 예시는 뉴스 기사들을 보내는 데이터 예시로, XML과 JSON의 차이를 보여주고 있다. 글자수가 JSON이 훨씬 적다. 

- **이러한 부분을 django에서도 구현하기 위해 클라이언트에게 이런 데이터들을 손쉽게 내려주기 위해서 JSONresponse라는 클래스를 지원한다.** 우리는 검색결과에 페이징 처리를 하기 위해 JSON으로 응답을 내려주는 부분을 만들어보자.


### 실습해보기
- 우리가 페이지네이션을 하기 위해서는, JSON을 리턴하는 별도의 View를 main.py에 구현을 해줘야 한다. 그러면 기존에 있던 코드들과 중복이 된다. 그래서 검색하고 필터링하는 로직 자체는 동일하기 때문에 하나의 함수로 빼주도록 하자. 기존의 SearchView 위쪽에 search라는 함수를 만들어준다.

```python
from django.views.generic import TemplateView, View
from django.http import JsonResponse     # JSON 응답을 위해 import
...

# Ajax를 위한 JSON 리턴 함수
def search(keyword, category_id, weekday, start_time, end_time, page_number):
    category = None

    query_sets = Restaurant.objects.filter(visible=True).order_by('-created_at') 
    if keyword:
        query_sets = query_sets.filter(Q(name__istartswith=keyword) | Q(address__istartswith=keyword))   # Q 오퍼레이터로 or 조건해주기
    if category_id:
        # category = Category.objects.get(pk=int(category_id))   기존의 방식
        category = get_object_or_404(Category, id=int(category_id))    # get_object_or_404를 활용한 방식
        query_sets = query_sets.filter(category=category)

    relation_conditions = None

    if weekday:
        # SELECT * FROM Restaurant r INNER JOIN RestaurantTable rt ON rt.restaurant_id = r.id
        # WHERE rt.weekday = :weekday
        relation_conditions = Q(restauranttable__weekday=weekday)

    if start_time:
        start_time = datetime.time.fromisoformat(start_time) # 12:00:00
        if relation_conditions:
            relation_conditions = relation_conditions & Q(restauranttable__time__gte=start_time)
        else:
            relation_conditions = Q(restauranttable__time__gte=start_time)

    if end_time:
        end_time = datetime.time.fromisoformat(end_time) # 12:00:00
        if relation_conditions:
            relation_conditions = relation_conditions & Q(restauranttable__time__lte=end_time)
        else:
            relation_conditions = Q(restauranttable__time__lte=end_time)
    
    if relation_conditions:
        query_sets = query_sets.filter(relation_conditions)

    
    restaurants = query_sets.distinct().all()
    paginator = Paginator(restaurants, 12)

    paging = paginator.get_page(page_number)

    # include한 search_bar.html를 위해 변수 생성
    # categories = Category.objects.all()

    return {
        'paging': paging,
        'selected_keyword': keyword,
        'selected_category': category,
        # 'categories': categories, 
        'selected_weekday': weekday,
        'selected_start': datetime.time.isoformat(start_time) if start_time else '',
        'selected_end': datetime.time.isoformat(end_time) if end_time else '',
    }


# 검색기능을 위한 View
class SearchView(TemplateView):
    template_name = 'main/search.html'

    def get_context_data(self, **kwargs):
        page_number = self.request.GET.get('page', '1')
        keyword = self.request.GET.get('keyword')
        category_id = self.request.GET.get('category')

        weekday = self.request.GET.get('weekday')
        start_time = self.request.GET.get('start')
        end_time = self.request.GET.get('end')

        return search(keyword, category_id, weekday, start_time, end_time, page_number)


# JSON을 리턴해주는 SearchView
class SearchJsonView(View):
    def get(self, request):
        page_number = self.request.GET.get('page', '1')
        keyword = self.request.GET.get('keyword')
        category_id = self.request.GET.get('category')

        weekday = self.request.GET.get('weekday')
        start_time = self.request.GET.get('start')
        end_time = self.request.GET.get('end')
        
        data = search(keyword, category_id, weekday, start_time, end_time, page_number)
        
        results = list(
            map(lambda restaurant: {
                "id": restaurant.id,
                "name": restaurant.name,
                "address": restaurant.address,
                "image": restaurant.main_image.image_url,
                "category_name": restaurant.category.name, 
            }, data.get('paging'))
        )
        
        return JsonResponse(results)
```


- **그리고나서 SearchView에 정의된 코드 중, category = None 이 부분부터 return 끝까지 모두 복사해서 search 함수에 붙여넣고 SearchView에서는 삭제하자. 그리고 SearchView에서는 return값으로 search 함수를 넣어준다. 이런식으로 중복되는 로직을 하나의 함수로 뺄 수 있다.**

- **그 다음으로 SearchView 밑에 JSON을 리턴해주는 SearchView를 하나 더 SearchJsonView라고 해서 만들어준다.** 이건 View를 상속받는 거니까 get과 request를 받아서 SearchView에서 데이터 받는 것과 비슷하게 코드를 작성해준다. 
  - **이 상태에서 이 값들을 JSON으로 변환을 해주는 것이다.** list() 함수 내부에서 map() 함수를 사용하고 lambda를 사용한다. **lambda는 iterable 하게 돌 수 있는 변수(리스트 등)를 가지고, 하나의 아이템을 어떤 데이터로 변환할 것인가를 정할 수 있다.** 우리가 작성한 코드 중에서 paging이라는 변수가 iterable 변수에 해당하기 때문에 이걸 다른 데이터로 변환할 수 있는 로직을 구현할 수 있다. 그래서 lambda 다음에는 iterable 변수에서 1개를 뺀 변수를 바로 입력해주면 된다. 지금은 restaurant이다.
  - 그리고 1개의 restaurant를 딕셔너리 형태로 바꿔주게 된다. **results 부터의 코드는 --> lambda식을 쓰는데, 먼저 data = search(keyword, category_id, weekday, start_time, end_time, page_number) 해당 코드의 오른쪽은 return 값이 딕셔너리이다. 그리고 lambda식을 쓸 때 이 딕셔너리 중에서, 어떤 데이터를 돌면서 변환을 할 것인가를 지정해줘야 하니까 --> data.get('paging') 이렇게 data라는 딕셔너리에 있는 paging이라는 key에 연결된 값, 객체를 가지고 오라고 설정한 것이다.** 
  - **그래서 paging에 있는 아이템을 하나씩 꺼내서 restaurant이라고 명명해서 꺼내와서 이걸 딕셔너리로 매핑을 해주는 것이다.** 즉, for restaurant in data.get('paging')과 같은 느낌이다. 

- 마지막에 return을 해서 JsonResponse를 설정해주면 되는데, 먼저 from django.http import JsonResponse 이렇게 import를 해주자. return 다음에 results를 넘겨주면 우리가 위에서 설정한 데이터들이 JSON 형태로 변환되서 내려가게 된다. JsonResponse를 command로 클릭해보면,

```python
class JsonResponse(HttpResponse):
    """
    An HTTP response class that consumes data to be serialized to JSON.

    :param data: Data to be dumped into json. By default only ``dict`` objects
      are allowed to be passed due to a security flaw before ECMAScript 5. See
      the ``safe`` parameter for more information.
    :param encoder: Should be a json encoder class. Defaults to
      ``django.core.serializers.json.DjangoJSONEncoder``.
    :param safe: Controls if only ``dict`` objects may be serialized. Defaults
      to ``True``.
    :param json_dumps_params: A dictionary of kwargs passed to json.dumps().
    """

    def __init__(self, data, encoder=DjangoJSONEncoder, safe=True,
                 json_dumps_params=None, **kwargs):
        if safe and not isinstance(data, dict):
            raise TypeError(
                'In order to allow non-dict objects to be serialized set the '
                'safe parameter to False.'
            )
        if json_dumps_params is None:
            json_dumps_params = {}
        kwargs.setdefault('content_type', 'application/json')
        data = json.dumps(data, cls=encoder, **json_dumps_params)
        super().__init__(content=data, **kwargs)
```

- 이렇게 나오듯이, JsonResponse는 인자로 data를 받고나서, data = json.dumps(data, cls=encoder, **json_dumps_params) 이렇게 json dumps라는 메소드를 써서 직렬화를 해준다. 근데 이 dumps를 command로 보면, 첫번째 인자인 object가 serialization이 가능해야 한다. 그게 가능해지려면 우리가 직접 JSONDecoder, serializer를 구현해도 되고 / 간단한 방법으로는, 딕셔너리를 넘겨버리면 serializer를 직접 구하지 않더라도 즉, 정확하게는 JSON을 어떻게 보여줄지를 매핑시키는 추가적인 로직을 구현하지 않고도, 우리가 전달한 딕셔너리 형태를 그대로 JSON형태로 변환하는 그런 로직이 실행되게 된다.** 그래서 이렇게 딕셔너리 형태로 하나씩 꺼내 변환을 해서 그걸 넘겨주는데, 문제없이 직렬화가 되서 JSON으로 넘어가게 하는 방법이라고 보면 된다.

- 다시 View로 돌아가면, 

```python
# JSON을 리턴해주는 SearchView
class SearchJsonView(View):
    def get(self, request):
        page_number = self.request.GET.get('page', '1')
        keyword = self.request.GET.get('keyword')
        category_id = self.request.GET.get('category')

        weekday = self.request.GET.get('weekday')
        start_time = self.request.GET.get('start')
        end_time = self.request.GET.get('end')
        
        data = search(keyword, category_id, weekday, start_time, end_time, page_number)
        
        results = list(
            map(lambda restaurant: {
                "id": restaurant.id,
                "name": restaurant.name,
                "address": restaurant.address,
                "image": restaurant.main_image.image_url,
                "category_name": restaurant.category.name, 
            }, data.get('paging'))
        )
        
        return JsonResponse(results, safe=False)
```

- JsonResponse에 safe도 설정해줘야 한다. 이 safe를 False로 두면, JsonResponse를 command로 클릭하면 나오는데, 딕셔너리 object가 직렬화가 될 것 같을 때만 이 safe를 수행하겠다라고 하는 것이다. 우리가 사실 실질적으로 딕셔너리가 아니라 results라는 리스트를 넣어주는 것이기 때문에, 혹시나 문제가 될 수 있기 때문에 딕셔너리가 아니지만 리스트를 넘겨주니까 safe를 False로 넘겨줘야지 리스트를 받을 수 있게 되는 것이다.


### 코드 정리
- 다른 클래스를 만들어서 검색 역할을 하는 클래스를 만들고 그 클래스를 믹스인에서 호출하도록 하면 search 코드를 분리할 수 있다. 
- 그래서 views 디렉터리 안에 service라는 패키지를 만들어준다. 그리고 그 안에 search.py라는 파일을 하나 만들어준다. 이 파일 안에서는,

```python
import datetime
from django.core.paginator import Paginator
from django.shortcuts import get_object_or_404
from ...models import Restaurant, Category
from django.db.models import Q

class RestaurantSearch:
    # Ajax를 위한 JSON 리턴 함수
    def search(self, keyword, category_id, weekday, start_time, end_time, page_number):
        category = None

        query_sets = Restaurant.objects.filter(visible=True).order_by('-created_at') 
        if keyword:
            query_sets = query_sets.filter(Q(name__istartswith=keyword) | Q(address__istartswith=keyword))   # Q 오퍼레이터로 or 조건해주기
        if category_id:
            # category = Category.objects.get(pk=int(category_id))   기존의 방식
            category = get_object_or_404(Category, id=int(category_id))    # get_object_or_404를 활용한 방식
            query_sets = query_sets.filter(category=category)

        relation_conditions = None

        if weekday:
            # SELECT * FROM Restaurant r INNER JOIN RestaurantTable rt ON rt.restaurant_id = r.id
            # WHERE rt.weekday = :weekday
            relation_conditions = Q(restauranttable__weekday=weekday)

        if start_time:
            start_time = datetime.time.fromisoformat(start_time) # 12:00:00
            if relation_conditions:
                relation_conditions = relation_conditions & Q(restauranttable__time__gte=start_time)
            else:
                relation_conditions = Q(restauranttable__time__gte=start_time)

        if end_time:
            end_time = datetime.time.fromisoformat(end_time) # 12:00:00
            if relation_conditions:
                relation_conditions = relation_conditions & Q(restauranttable__time__lte=end_time)
            else:
                relation_conditions = Q(restauranttable__time__lte=end_time)
        
        if relation_conditions:
            query_sets = query_sets.filter(relation_conditions)

        
        restaurants = query_sets.distinct().all()
        paginator = Paginator(restaurants, 12)

        paging = paginator.get_page(page_number)

        # include한 search_bar.html를 위해 변수 생성
        # categories = Category.objects.all()

        return {
            'paging': paging,
            'selected_keyword': keyword,
            'selected_category': category,
            # 'categories': categories, 
            'selected_weekday': weekday,
            'selected_start': datetime.time.isoformat(start_time) if start_time else '',
            'selected_end': datetime.time.isoformat(end_time) if end_time else '',
        }
```


- **먼저 코드를 위한 라이브러리 및 모델 등을 import 해준다. 그리고 RestaurantSearch 라는 클래스르 정의하고 그 안에 main.py에 있던 search 함수 자체를 가져와서 붙여준다. main.py에서는 아예 삭제시킨다. 그리고 클래스 내부의 함수이니까 def search(self, keyword, category_id, weekday, start_time, end_time, page_number) 이렇게 첫 인자를 self로 넣어준다.**

- **그 다음, 다시 main.py로 가서 SearchView 클래스가 우리가 방금 생성한 클래스를 상속받게끔 하자.**

```python
from service.search import RestaurantSearch  # RestaurantSearch 클래스를 상속받기 위함
...

# 검색기능을 위한 View
class SearchView(TemplateView, RestaurantSearch):
    template_name = 'main/search.html'

    def get_context_data(self, **kwargs):
        page_number = self.request.GET.get('page', '1')
        keyword = self.request.GET.get('keyword')
        category_id = self.request.GET.get('category')

        weekday = self.request.GET.get('weekday')
        start_time = self.request.GET.get('start')
        end_time = self.request.GET.get('end')

        return self.search(keyword, category_id, weekday, start_time, end_time, page_number)


# JSON을 리턴해주는 SearchView
class SearchJsonView(View, RestaurantSearch):
    def get(self, request):
        page_number = self.request.GET.get('page', '1')
        keyword = self.request.GET.get('keyword')
        category_id = self.request.GET.get('category')

        weekday = self.request.GET.get('weekday')
        start_time = self.request.GET.get('start')
        end_time = self.request.GET.get('end')

        data = self.search(keyword, category_id, weekday, start_time, end_time, page_number)

        results = list(
            map(lambda restaurant: {
                "id": restaurant.id,
                "name": restaurant.name,
                "address": restaurant.address,
                "image": restaurant.main_image.image_url,
                "category_name": restaurant.category.name, 
            }, data.get('paging'))
        )

        return JsonResponse(results, safe=False)
```

- 그러기 위해 먼저 RestaurantSearch를 Import 해준다.
- 그리고 class SearchView(TemplateView, RestaurantSearch) 이렇게 RestaurantSearch를 상속받고, 그냥 return search가 아니라 상속받은 클래스의 함수이니까 return self.search라고 수정해주자.
- 또한, 그 밑에 있는 JSON을 리턴해주는 SearchView인 SearchJsonView도 똑같이 수정해준다.


### Ajax 호출 구현해보기

18:19
