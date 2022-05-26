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
- template 디렉터리 내부 - main 디렉터리 - search.html로 가자. 검색결과 section 위에 Ajax 페이징 시 기존 쿼리정보를 저장해두는 input type hidden를 구현해준다.

```html
{% extends "common.html" %}
{% load static %}
{% load search_tags %}
...

<!-- Ajax 페이징 시 기존 쿼리정보 -->
<input type="hidden" id="result-keyword" value="{{ selected_keyword }}">
<input type="hidden" id="result-category" value="{{ selected_category.id }}">
<input type="hidden" id="result-weekday" value="{{ selected_weekday }}">
<input type="hidden" id="result-start" value="{{ selected_start }}">
<input type="hidden" id="result-end" value="{{ selected_end }}">

<section class="mt-4">
    <h4>검색 결과</h4>
    <div class="row" id="search_result">
...
```

- **이렇게 입력하면, 이 페이지에서 input 값들을 자바스크립트에서 접근할 수 있게 만들기 위해 input 태그를 hidden으로 설정해주는 것이다.** 이건 Ajax를 구현할 때 기본적으로 많이 사용하는 방식이다.
  - 이렇게 hidden으로 해두면 사용자 눈에는 안 보이지만, 자바스크립트는 이 값을 가져갈 수 있으니까 이 값을 읽어서 다음 페이징을 연결해서 호출할 때 쿼리 파라미터를 붙일 수 있게 해준다.
- 그리고 해당 html 위쪽에 load static를 해준다. 강의 자료의 search.js를 가져와준다.
  - 해당 파일을 보면, Ajax를 호출하는 함수를 만들어놓았다. 똑같이 result-keyword 등 검색결과들을 다 가져오는 코드이다. 

```javascript
var currentPage = 1;
var isEndOfScroll = false;

window.onload = function() {
  window.addEventListener('scroll', function(event)
  {
      var element = window.document.scrollingElement;
      if (element.scrollHeight - element.scrollTop === element.clientHeight && !isEndOfScroll)
      {
          fetchData();
      }
  });
};

function fetchData() {
    var keyword = document.getElementById('result-keyword').value;
    var category = document.getElementById('result-category').value;
    var start = document.getElementById('result-start').value;
    var end = document.getElementById('result-end').value;
    var weekday = document.getElementById('result-weekday').value;

    currentPage ++;

    var httpRequest = new XMLHttpRequest();
    httpRequest.addEventListener("load", (e) => {
        var jsonResponse = JSON.parse(e.target.responseText);
        console.log(jsonResponse)
        jsonResponse.forEach(data => appendItem(data));
        if (jsonResponse.length < 8) isEndOfScroll = true;
    });
    httpRequest.open("GET", "/search/json/?page=" + currentPage
        + "&keyword=" + keyword + "&category=" + category + "&start=" + start + "&end=" + end + "&weekday=" + weekday);
    httpRequest.send();
}

function appendItem(data) {
    var template = document.getElementById("restaurant-template");
    var body = document.getElementById("search-result")
    var clone = template.content.cloneNode(true);
    clone.querySelector(".item-category").textContent = data.category_name;
    clone.querySelector(".item-name").textContent = data.name;
    clone.querySelector(".item-address").textContent = data.address;

    if (clone.querySelector(".item-link") != null) {
        var link = clone.querySelector(".item-link").href;
        link = link.replace("/0/", "/" + data.id + "/");
        clone.querySelector(".item-link").href = link;
    }

    var finalUrl = data.image.replace("/media/https%3A", "https:/"); // 외부 url 대응
    clone.querySelector(".item-image").src = finalUrl;

    body.appendChild(clone);
}
```

- **search_bar.html에 있는 form에 있는 데이터를 읽어오면 안되냐고 생각할 수 있는데, 여기 있는 걸 읽어오게 되면 기존의 검색결과를 가지고 페이지네이션 하는 게 아니라, 사용자가 여기 값을 키워드를 바꿀 수도 있고 시간을 바꿀 수도 있다. 그렇기 때문에 다른 페이지네이션이 되게 된다. 그래서 그걸 사용자가 건들지 못하는 hidden 필드에 숨겨놓고 불러오는 것이다.**
- 위의 코드를 설명하면, fetchData() 함수에서 키워드에 대한 검색 조건들을 불러와서 currentPage ++; 이렇게 페이지를 하나 더한 다음에, var httpRequest = new XMLHttpRequest(); 이렇게 Ajax 요청을 보낸다. 그리고 httpRequest.open("GET", "/search/json/?page=" + currentPage + "&keyword=" + keyword + "&category=" + category + "&start=" + start + "&end=" + end + "&weekday=" + weekday); 이렇게 쿼리를 만드는 코드가 있다. 그리고 마지막은 function appendItem(data) { 이것 이하로 template를 사용해서 데이터를 받고 clone를 한 다음에 원하는 값들을 채워준다.
- 앞쪽에는 window 로드를 할 때, 스크롤이 될 때 마다 이벤트를 받아서 그 값이 스크롤이 맨 밑까지 내려왔을 때, fetchData(); 가 실행되도록 설정.
- 이 해당 파일을 web 내부 static 디렉터리 내부에 search.js라고 해서 만들어준다.

* * *
- **그 다음으로는 search.html에다가 section 아래쪽에 script를 넣어준다.**

```html
    </div>
</section>

<script src="{% static 'search.js' %}"></script>
<template id="restaurant-template">
    <div class="col-sm-3 mb-2 mt-2">
        <div class="card">
            <div class="card-header item-category">
            </div>
            <img src="" class="card-img-top item-image">
            <div class="card-body">
                    <h5 class="card-title itme-name"></h5>
                    <p class="card-text item-address"></p>
            </div>
        </div>
    </div>
</template>
```

- **이렇게 js파일을 불러오고, template 요소를 사용할 것들을 미리 구성해야 한다. 그래서 template 태그를 사용하고 그 안에는 기존의 card 코드를 재사용해준다. class값도 조금 더 추가해주었다.** 
- Ajax를 쓰다보면, 자바스크립트를 보면 Ajaxf를 호출할 때, + "&keyword=" + keyword + "&category=" + category + "&start=" + start + "&end=" + end + "&weekday=" + weekday); **이렇게 Ajax를 호출할 때, 값을 어쩄든 쿼리 파라미터로 보내주긴 한다. 그런데 여기에 keyword나 category, start 이런애들이 공백문자열로 올 수가 있다.** 
  - 그래서 추가적인 처리가 필요하다. search.py에 RestaurantSearch 클래스 내부에있는 weekday만 있는게 아니라, and 조건으로 

```python
class RestaurantSearch:
    # Ajax를 위한 JSON 리턴 함수
    def search(self, keyword, category_id, weekday, start_time, end_time, page_number):
        category = None

        query_sets = Restaurant.objects.filter(visible=True).order_by('-created_at') 
        if keyword and len(keyword) > 0:
            query_sets = query_sets.filter(Q(name__istartswith=keyword) | Q(address__istartswith=keyword))   # Q 오퍼레이터로 or 조건해주기
        if category_id and len(category_id) > 0:
            # category = Category.objects.get(pk=int(category_id))   기존의 방식
            category = get_object_or_404(Category, id=int(category_id))    # get_object_or_404를 활용한 방식
            query_sets = query_sets.filter(category=category)

        relation_conditions = None

        if weekday and len(weekday) > 0:
            # SELECT * FROM Restaurant r INNER JOIN RestaurantTable rt ON rt.restaurant_id = r.id
            # WHERE rt.weekday = :weekday
            relation_conditions = Q(restauranttable__weekday=weekday)

        if start_time and len(start_time) > 0:
            start_time = datetime.time.fromisoformat(start_time) # 12:00:00
            if relation_conditions:
                relation_conditions = relation_conditions & Q(restauranttable__time__gte=start_time)
            else:
                relation_conditions = Q(restauranttable__time__gte=start_time)

        if end_time and len(end_time) > 0:
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

- **weekday의 문자열 길이가 0보다 컸을 때 까지도 검새를 해줘야 된다.** 그리고 start_time 함수문에서도 똑같이 수정해준다. end_time도 마찬가지이다. keyword 부분과 category_id도 동일하게 수정해준다. 이렇게 제약 조건을 하나씩 두는 것이다.

* * *
- 그리고 이제 urls.py를 추가로 설정해준다. table_booking 디렉터리 내부에 urls.py로 가서, 

```python
from web.views.main import IndexView, SearchView, SearchJsonView
...

urlpatterns = [
    ...
     # Ajax 관련 URL
    path('search/json/', SearchJsonView.as_view(), name='search-json'),
]
```

- **먼저 SearchJsonView를 가져와준다. 그리고, SearchJsonView에 as_view함수를 사용해서 호출해준다.**

* * * 
## 페이지네이션 실습
- 실제 우리가 페이지네이션을 보려면 데이터가 많아야 한다. 먼저 DBeaver로 들어가보자. 그 중에서도 web의 restaurant 모델을 클릭해보자. 여기서 동일한 내용을 엄청 복사해서 총 23개의 행이 되도록 해보자.
- 또한, 이미지도 많이 만들어줘야 한다. 그래서 restaurantimage 모델에 클릭해서 똑같은 개수만큼 데이터를 생성해준다. 그리고 restaurant_id 필드에 순서대로 번호를 채워준다.
- 그리고 다시 web의 restaurant 모델에서 main_image_url를 순서대로 넣어주자.

- 오류가 날 수 있는 부분들을 잡고가자. main.py에서 

```python
from .service.search import RestaurantSearch  # RestaurantSearch 클래스를 상속받기 위함
...

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
                "image": str(restaurant.main_image.image),
                "category_name": restaurant.category.name, 
            }, data.get('paging'))
        )

        return JsonResponse(results, safe=False)
```


- **이렇게 RestaurantSearch를 가지고 오기 위해서 점을 1개 찍어야 하고, SearchJsonView에서는 "image": str(restaurant.main_image.image), 이렇게 image 데이터 관련해서, imageField 같은 경우에는 우리가 string으로 변환해주지 않으면 오류가 나게 된다.** 


- 그리고 자바스크립트 파일을 보면,

```javascript
function fetchData() {
    var keyword = document.getElementById('result-keyword').value;
    var category = document.getElementById('result-category').value;
    var start = document.getElementById('result-start').value;
    var end = document.getElementById('result-end').value;
    var weekday = document.getElementById('result-weekday').value;

    currentPage ++;

    var httpRequest = new XMLHttpRequest();
    httpRequest.addEventListener("load", (e) => {
        var jsonResponse = JSON.parse(e.target.responseText);
        console.log(jsonResponse)
        jsonResponse.forEach(data => appendItem(data));
        if (jsonResponse.length < 4) isEndOfScroll = true;
    });
    httpRequest.open("GET", "/search/json/?page=" + currentPage
        + "&keyword=" + keyword + "&category=" + category + "&start=" + start + "&end=" + end + "&weekday=" + weekday);
    httpRequest.send();
}
```

- 위에서 if (jsonResponse.length < 4) isEndOfScroll = true; 이렇게 JsonResponse가 4보다 작으면, 사실상 페이지의 끝이라고 판단하는 로직이 있다. 
  - 그래서 추가로 search.py로 가서 class RestaurantSearch: 부분을 수정한다. 한 페이지당 4페이지가 보이게끔 해준다.

```python
class RestaurantSearch:
    # Ajax를 위한 JSON 리턴 함수
    def search(self, keyword, category_id, weekday, start_time, end_time, page_number):
        category = None
        
    restaurants = query_sets.distinct().all()
    paginator = Paginator(restaurants, 4)

    paging = paginator.get_page(page_number)
    ...
```

* * *
### 서버 구동하기
- **위의 설정을 모두 마친 후, 서버를 가동해보자. 사이트의 첫페이지에서 그냥 검색을 해보면, 4개씩 스크롤 할 때마다 떠야 하는데 나는 안된다...**
- 만약 해상도가 크게 되면, 스크롤이 안되는 경우가 있다. 왜냐면 화면의 모든 영역이 이미 다 보여지고 있으니까 스크롤이 안되서 스크롤 이벤트가 발생이 안되고 그러면 Ajax로 인한 페이지네이션이 발생이 안되게 된다. 이 부분은 해결해보자.
  - 다시 자바스크립트에서 **if (jsonResponse.length < 8) isEndOfScroll = true;** 이렇게 8로 수정 --> 이렇게 하면 이제 8개씩 보여줄 것이기 때문에 8개 보다 적은 개수가 로딩이 되나면, 이제 스크롤이 끝났다 라고 볼 수 가 있는 것이다.
  - 그리고 "페이지네이션"이라는 객체는 큰 값이 들어왔을 때, 페이지가 오버가 되면 그냥 가장 라스트 페이지를 보여주는 그런 이슈가 있다. search.py에 RestaurantSearch 클래스 내부에 정의되어 있는 paginator = Paginator(restaurants, 8) 객체 입니다. 8을 넣어주는 것으로 감사하다.


- 자바스크립트 같은 경우에는, 브라우저에 캐시가 남아서 자동으로 우리가 수정한 코드가 맵 브라우저에 갱신이 안 된다. 그래서 개발자도구를 켜서 네트워크 - "캐시사용 중지"를 누르고 다시 서버를 가동시키면 8개씩 로딩이 되어야 하는데 나는 안 된다.


### CORS
- 우리가 지금 Ajax를 배웠는데, 추가로 알아야 하는 개념이 더 있다. 
- 기본적으로 Ajax 요청을 교차 출처를 허용하지 않는다. 하지만 교차 출처 요청에서도 자원을 공유할 수 있도록 정책을 설정하는 것을 CORS라고 한다.

- **현재 랜더링되고 있는 호스트에서 다른 외부 도메인으로 Ajax 요청을 보내게 될 경우, Ajax 요청이 실패하는 경우가 있다. 이 때 이걸 허용해주기 위한 개념이 CORS라고 되는 것이다.** '교차 출처 리소스 공유' 라는 뜻으로 서로 다른 도메인이나 웹사이트 등에서 생성된, 제한된 데이터를 공유할 수 있도록 하는 동작방식이다. 
- 기본적으로 Ajax를 기반으로 이우어지는 Http 요청들은 교차출처, 그러니까 크로스 오리진을 허용하지 않는다. 즉, 지금 접속하고 있는 사이트가 아닌, 다른 사이트에 정보를 가져오거나, 데이터를 전송하는 걸 막고 있다. 
- **이러한 이유는 바로 '보안'때문이다.** 만약 이걸 허용하게 된다면, 내가 만든 사이트의 정보를 다른 사이트에서 아무 문제 없이 사용자가 가져다 쓸 수 있게 된다. 
  - 특히, 클라이언트 요청의 경우 불특정 대다수의 IP로 접근하기 때문에 서버쪽에서 제어하기가 어렵다는 점이 있다.
  - **CORS는 이러한 기본정책을 허용하기 위해서 만들어진 약속으로, Http 응답 헤더에 accesscontrolalloworigin, acesscontrolallow 메소드 등의 데이터를 헤더에 추가해서 --> 허용된 사이트에서 Ajax 호출로 데이터에 접근할 수 있도록 한다.**   


### CORS 동작 과정
- 바탕화면 캡처 화면에서 웹 브라우저는 Ajax 호출을 진행하기전에 요청주소의 도메인이 현재 접속한 주소와 다르다면 get이나 post가 아닌 OPTIONS라는 특수한 요청 메소드로, 서버한테 Ajax 요청이 가능한지 물어본다. 마치 노크를 하는 것처럼 말이다. 
- 이에 대해 서버는 CORS 정책을 내려준다. 이 데이터는 Http 응답 헤더에 포함해서 돌려주는데, 헤더 키 같은 데이터를 알려준다. 
- 이렇게, 외부 사이트의 데이터를 가지고 오고 싶은 경우에는 CORS 정책을 허용하는 서버에서만 데이터를 받아올 수 있고, 기본적으로는 보안상의 이유로 같은 도메인의 주소로만 요청이 가능하다. 
- 요즘은 프론트의 포싱되는 도메인과 백엔드의 도메인이 다른 경우가 많다. 그래서 특정 도메인에 대해서 CORS를 열어주려면 ..코드를 입력하면 된다.
  - 이걸 실습으로 진행해보자.


```terminal
pip install django-cors-headers
```


- 먼저 설치하기. 그리고 settings.에 가서 INSTALLED_APPS에다가 'corsheaders', 이렇게 추가해주기. 그 다음 # all auth 밑에다가는 

```terminal
CORS_ORIGIN_WHITELIST = [
    'http://localhost:8000',
    'http://127.0.0.1:8000',
]

ORS_ORIGIN_ALLOW_ALL = True

CORS_ALLOW_CREDENTIALS = True

CORS_ALLOW_METHODS = [
    'DELETE', 'GET', 'OPTIONS', 'POST', 'PUT'
]

CORS_ALLOW_HEADERS = [
    'accept',
    'accept-encoding',
    'authorization',
    'content-type',
    'origin',
    'user-agent',
    'x-csrftoken', 
    'x-requested-with',
]


MIDDLEWARE = [ 
    'corsheaders.middleware.CorsMiddleware'
```


- **이 리스트에 CORS를 허가할 외부의 도메인들을 기입해주면 된다. 즉, 내가 Ajax 요청을 받아줄 오리진, 클라이언트에서 랜더링하고 있는 페이지의 호스트 주소를 입력해주면 허가가 되는 것이다.** 
- 그 다음에는 CORS_ALLOW_CREDENTIALS를 True로 해줘야 쿠키라던가 세션 공유들을 할 때 이걸 False로 하게 되면 --> CORS 요청할 때 세션이 유지되지 않는 문제가 있다. 그래서 쿠키나 세션을 CORS 요청해서도 어느정도 공유가 되도록 하기 위해서 해당 변수를 True해주면 좋다.
- 그 다음 MIDDLEWARE 쪽에는 'corsheaders.middleware.CorsMiddleware' 이렇게 추가한다. 그래야 세팅이 완료된다. 
- 만약, WHITELIST를 구상하고 싶지 않다면 ORS_ORIGIN_ALLOW_ALL = True --> 이렇게 하면 도메인을 지정하지 않고도 모든 요청에 대해서 다 허용을 하게 한다. 
- 그리고 또 하나는, CORS_ALLOW_METHODS 라는 게 있다. 여기서는 내가 CORS 요청을 허용할 메소드들을 정리할 수 있다. 
- 그리고 또, CORS_ALLOW_HEADERS 것도 있다. Ajax를 교차출처로 요청하는 경우에 헤더 값들도 제한을 둘 수 있는 것이다. 그래서 몇몇 정해진 헤더값 외에는 보낼 수가 없게 되어있어서 그런 예외적인 헤더를 보내게 되면 또 블락킹이 된다. 그래서 이걸 막아주기 위해 설정. 내가 허용하고 싶은 헤더값들도 명시할 수 있다.
