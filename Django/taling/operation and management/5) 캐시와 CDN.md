## 캐시와 CDN(바탕화면 참고)

## 캐시
- 새로운 영화가 개봉되었다고 하자. 이 결말을 알려면 영화관에 가야 한다. 근데 우리의 친구가 먼저 그 영화를 봤다면, 결말을 알고 있을 것이다. 이걸 우리가 스포당하면 영화안봐도 결말을 알 수 있게 된다. 
- 캐시가 이 친구의 역할과 비슷하다고 볼 수 있다. 어떤 데이터 요청에 대해서 굳이 원본 소스를 탐색하지 않더라도 요청하는 자원에게 더 가까운 컴퓨팅 자원이 기억해두었다가 빠르게 데이터를 돌려주는 것이다. 
- 웹 서비스의 관점에서는, 여러 형태로 존재할 수 있다. 
  - DNS 정보의 캐시 : 도메인이 가리키는 IP 주소등의 정보를 컴퓨터가 기억 
  - 웹 브라우저의 캐시 : 이미지, css, js 등의 정적 파일들을 웹 브라우저가 보존하는 것
  - 네트워크 트래픽 상에서의 캐시 : 콘텐츠 파일들을 원본을 가지고 있는 서버가 아닌 네트워크 위치가 가까운 서버에서 대신 전송하는 개념(CDN)
  - 데이터베이스 캐시 : 어플리케이션 메모리 혹은 인메모리 데이터베이스를 활용해서 데이터베이스에 접근하지 않고 메모리상의 데이터베이스 안에 있는 정보를 대신 끄집어내는 것.


## CDN
- 네트워크 트래픽 상에서의 캐시 개념은, CDN 그러니까 Content Delivery Network 개념으로 활용이 된다.
  - 지역 별 콘텐츠 서버를 구축 후에 거점 네트워크 내에 콘텐츠를 제공하는 시스템이다. Cloudflare, AWS Cloudfront 등이 있다. 

- **만약에, 어떤 1KB의 파일을 받아야 하는데 그걸 받을 수 있는 서버가 미국에 있다면 우리의 http 요청을 어떻게 전달하게 될까?**
  - 국내의 네트워크 회선을 따라서 한국 최상위 라우터로 접근을 해서, 태평양을 건너서 미국으로 진입한뒤에 다시 지역 네트워크로 들어가서 최종적으로 미국 서버에 도달하고나서 -> 다시 반대방향으로 되돌아와야
    한다. 이렇게 긴 거리를 이동하게되면, 당연히 데이터 손실 가능성이 높아지고 많은 네트워크 지점들을 거쳐야 하기 때문에 오래 걸리게 된다. 그리고 전체 회선에 더 많은 트래픽을 유발하게 된다.  
   - CDN은 이런것을 보완하기 위해서 콘텐츠의 원본 소스 서버가 각 국가별 또는 지역별 네트워크 서버(Edge)에 contents를 미리 배포해두고 해당 지역의 컴퓨터가 데이터를 요청하면 -> 해당 지역 네트워크 내의 
     서버 데이터를 대신 가져가게 하는 것을 의미한다.  
     
- **반면에, 데이터베이스 캐시란 : 데이터베이스에 저장되는 동적인 데이터이지만 자주 변하지 않는 값일 때는 유용하게 사용할 수 있다.** 
  - 기본적으로 데이터베이스는 디스크에서 데이터를 읽기 때문에 I/O 작업의 부하가 컴퓨터 구조 내에서 큰 편이고 느리기도 한다. 
  - 예전에는 디스크라고 하면 실린더, 헤드, 암 등의 물리적인 장치들 로 이루어지기 때문에 자기판을 돌려서 자성의 변화등을 이용해서 데이터를 읽거나 쓰는 방식이었다. 그래서 실제 물리적인 회전과 헤드의 이동등이 
    발생해야 하기 때문에 데이터의 처리가 오래 걸렸다. 
  - **지금은 SSD가 나와서 "셀"이라는 단위의 전압을 직접 가하는 식으로 저장하고 전류를 통해서 데이터를 읽는 등 빨라지기는 했다.** 하지만 그렇다하더라도 램보다는 빠를 수 없다. CPU 입장에서는 바이트 단위로 데이터를 처리하기 때문에 이 0과 1값들을 직접적으로 전류의 순환을 통해서 저장할 수 있고 편하게 읽을 수 있는 램이 주 작업대, 책상 역할을 한다고 볼 수 있다.
  - 구조적으로 내부에 트랜지스터와 플립플롭 이라는 것들로 이루어져 있는게 대표적인 특징인데 일반적인 램들은 전류가 계속해서 순환해야지 플립플롭의 전류 전달 방향이 유지가 되서 데이터가 유지되고 있다. 
  - 그래서 아무래도 자주 바뀌는 것이 아니라면 데이터베이스를 직접 읽어오는 것 보다는, 중간에 메모리에 저장해두고 유효기간을 두어서 유효기간이 지나면 그 때 서야 데이터베이스에 정보를 갱신하는 것이 효과적으로 데이터베이스의 부하를 줄여주게 된다. 
  - **우리는 이 메모리 캐시를 Redis를 이용해서 구현해보려고 한다.** 최근에 django 4.0으로 업데이트 되면서 레디스 캐시를 쉽게 설정할 수 있게 되었다. 


## 실습 진행
- 먼저 지금은 django가 3.9버전이기 때문에 올려줘야 한다.

```terminal
python -m pip install -U Django==4.0
```

- 이렇게 업그레이드 시켜주자.

- **그리고 이제 settings.py로 가서 코드를 추가해준다.**

```python
...


# cache  
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': 'redis://:passw0rd!**@15.165.204.210:6379'
    }
}
```

- 이렇게 입력해주면 연동이 된다. 그리고 LOCATION에는 암호를 입력해줘야 한다. 암호를 시작할 때는 : 콜론을 넣고 시작하면 된다. 그리고 비밀번호 다음에는 @ 다음, host 주소를 입력해줘야 한다. 그 다음에는 : 콜론 다음에 port번호를 넣어준다. 

- **이제 캐시를 조금 더 디테일하게 설정을 해보자. 먼저 url 기반의 캐시를 설정해보자.**
- 우리 프로젝트 Web앱의 urls.py로 가서, IndexView를 사용한 인덱스 화면에다가 --> 인덱스 html 전체를 캐시를 먹여보자.

```python
from django.views.decorators.cache import cache_page
...

urlpatterns = [
    # 메인 페이지
    path('', cache_page(60 * 15)(IndexView.as_view()), name='index'),
    ...
    
```

- **이렇게 cache_page를 import 하고 IndexView 전체를 cache_page로 감싸준다.**
  - **여기서 60 * 15는 캐시가 유지되는 시간, 즉 유효기간을 입력해준다. 이건 초단위라서 지금은 15분마다 갱신되도록 설정했다. IndexView.as_view()를 그대로 캐시로 저장하도록 설정했다.**

- 여기까지 해놓고 서버를 구동시키고 사이트에 들어가면 -> http://localhost:8000/ 로 보여지는 메인 화면이 이미 캐시에서 한 번 먹여진 것이다. 그래서 내가 처음으로 페이지에 접근을 하는 순간, 일단 처음에는 캐시의 데이터가 없어서 이 페이지를 일단 랜더링을 한다. 그리고나서 내가 한 번에 이 페이지에 다시 접근할 때는 속도가 조금 더 빨라진게 느껴진다. 원래보다 절반 이상이 빨라진다.

- **이걸 터미널에서 확인하려면 다시 Redis 서버에 접속해야 한다.**
  - **다시 접속할 때 안되면 --> AWS 인바운드 규칙에서 SSH인 22포트를 모든 IP에서 접근할 수 있도록 수정해주자.** 접속한 다음,

```terminal
redis-cli

127.0.0.1:6379> AUTH passw0rd!**
OK

127.0.0.1:6379> KEYS *
1) "session:uqst3hgooehlw638785lveuglmyhlr2s"

127.0.0.1:6379> KEYS *
1) ":1:views.decorators.cache.cache_page..GET.cb4fbe6a5f6caec1f1715b85fecd2d7f.d41d8cd98f00b204e9800998ecf8427e.en-us.UTC"
2) ":1:views.decorators.cache.cache_header..cb4fbe6a5f6caec1f1715b85fecd2d7f.en-us.UTC"
3) "session:uqst3hgooehlw638785lveuglmyhlr2s"
```

- 이렇게 메인페이지에 캐시를 걸고 새고로침 해주면 --> Redis 서버에 위와 같이 캐시가 남아있는 것을 볼 수 있다. 그 다음에, 이 데이터를 끄집어 내보면,

```terminal
127.0.0.1:6379> GET :1:views.decorators.cache.cache_page..GET.cb4fbe6a5f6caec1f1715b85fecd2d7f.d41d8cd98f00b204e9800998ecf8427e.en-us.UTC

```

- **이렇게 GET 다음에 위에서 1번 캐시를 입력해주면, html이 그대로 캐싱되어있는 것을 볼 수 있다.**

* * *
- 위에까지는 전체 페이지에 대해서 캐시를 먹인 것이다. 그래서 자세하게 로그인, 로그아웃이나 예약 내역 등의 페이지들은 상황에 따라서 캐시를 안 먹어야 하는 경우도 있다. 그래서 지금 로그아웃을 눌러보면, 로그인으로 버튼이 바뀌는 게 아니라 그대로 로그아웃으로 남아있다. 이건 오류가 아니라, 캐시를 먹어서 캐시된 페이지가 자꾸 뜨기 때문에 내가 로그아웃을 했는데도 이전 페이지가 계속 뜨는 것이다. 그래서 

```python
urlpatterns = [
    # 메인 페이지
    # path('', cache_page(60 * 15)(IndexView.as_view()), name='index'),
    path('', IndexView.as_view(), name='index'),
```

- **이렇게 설정한 url를 원복시켜주자. 그래도 일단 캐시 먹인 url은 주석처리로 남겨두자.**
- 캐시를 먹일만한 것은, 고정된 게시판 같은 경우 적절하다. 그런데 이렇게 로그인/로그아웃 부분은 문제가 있다. 


### Template의 일부를 잘라서 캐시 적용하기
- 우리가 templates - restaurant - detail.html로 들어가보면, 후기가 뜨는 곳이 있다. 여기에 캐시를 먹여보자.
  - 이 페이지 전체를 먹이는 건 아니다.

```html
{% load cache %}
...

<!-- 리뷰리스트 보여주기 -->
<section class="mt-4">
    <div class="row">
        <div class="col-12 list-group">
            <!-- 별점 평균 -->
            <h6>후기
                {% if ratings.ratings__avg %}
                | {{ ratings.ratings__avg }}    
                {% endif %}
            </h6>

            <!-- 후기 리스트를 캐시로 감싸기 -->
            {% cache 60 detail-reviews restaurant.id %}
            <!-- 리스트 출력 -->
            <div class="list-group">
                {% if reviews %}
                    {% for review in reviews %}
                    <div class="list-group-item list-group-item-action">
                        평점: {{ review.ratings }}<br>
                        {{ review.comment }}
                    </div>
                    {% endfor %}
                {% else %}
                    <div class="list-group-item list-group-item-action">
                        후기가 없습니다.
                    </div>
                {% endif %}
            </div>
            {% endcache %}
        </div>
    </div>
</section>
              
```

- 처음에 일단 {% load cache %}로 가져온다.
- {% cache 60 detail-reviews restaurant.id %} 이렇게 후기 리스트가 나오는 부분 위쪽에 코드를 작성해준다. cache라는 걸 사용할 수 있고 60초로 설정 / detail-reviews라는 이름으로 캐시를 남길 것이다. 이 캐시에 추가적으로 restaurant.id에 따라서 키가 달라지도록 파라미터를 붙일 수 있다. 이렇게 하면 detail-reviews라는 이름과 id가 조합되어서 캐시의 키가 완성이 된다.
- 디테일 페이지가 레스토랑마다 다르게 떠야 하니까 그래서 레스토랑마다 다른 키로 캐시가 먹이도록, 레스토랑마다 캐시가 따로 저장이 되도록 구현을 한 것이다.
- 그리고 마지막으로  {% endcache %} 로 닫아준다. 그래서 해당 부분만 캐시로 지정이 된다. 


- 실제로 리뷰 부분을 브라우저로 들어가고 redis 서버를 확인해보면 --> 1) ":1:template.cache.detail-reviews.247730f9d0d2eaad265a470e32aa0cdf" 이렇게 detail 관련 키가 나온다.
- 이걸 위와 같이 GET :1:template.cache.detail-reviews.247730f9d0d2eaad265a470e32aa0cdf 이렇게 입력해보면 --> 일부분만 캐시로 들어간걸 볼 수 있다. 


### 데이터베이스 캐시
- 이 데이터베이스 캐시는 우리가 적합하게 쓸 곳이 있다. 바로 검색 결과 부분이다. service - search.py로 가서 RestaurantSearch 클래스가 있는데 여기서 구현을 해보자.

```python
from django.core.cache import cache
...

class RestaurantSearch:
    def create_cache_key(self, keyword, category_id, weekday, start_time, end_time, page_number):
        return "%s:%s:%s:%s:%s:%s" % (keyword, category_id, weekday, start_time, end_time, page_number)
        
        
    # Ajax를 위한 JSON 리턴 함수
    def search(self, keyword, category_id, weekday, start_time, end_time, page_number):
        cache_key = self.create_cache_key(keyword, category_id, weekday, start_time, end_time, page_number)
        cached_data = cache.get(cache_key)
        if cached_data:
            return cached_data
```

- RestaurantSearch 클래스 내부에 메소드를 하나 더 생성한다. create_cache_key라는 메소드로 캐시 키를 생성하는 메소드이다. 똑같이 search 메소드에서 받는 인자들을 받는 것으로 해준다. 그래서 받는 인자들의 조합으로 캐시 키를 만들면 된다.
- return "%s:%s:%s:%s:%s:%s" 이렇게 인자들의 조합으로 키를 6개 붙여가지고 캐시 키를 만들면 된다. 
- 그리고 기존의 search 메소드 내부에 넣어준다.
  - cache_key라는 변수를 정의하고 create_cache_key 메소드를 호출해서 가져온다. 그리고 cached_data가 있는지 체크한다. cached_data = cache.get(cache_key) 이렇게 cache의 get를 사용해서 캐시 키로 조회를 한다. 
  - **그래서 cached_data가 있으면 그대로 return 해주면 -> 굳이 밑에 있는 DB를 히트치는 로직을 하지 않고도 데이터를 조회할 수 있게 된다.**
  - 그리고 캐시가 없을 경우에는 -> 밑에 로직들을 따라가서 데이터를 다 조합한 다음에 마지막에 return 하기전에 캐시에 저장하는게 나와있다. 
  - 일단 보내는 변수들을 바로 return 하지말고 

```python
result = {
            'paging': paging,
            'selected_keyword': keyword,
            'selected_category': category,
            # 'categories': categories, 
            'selected_weekday': weekday,
            'selected_start': datetime.time.isoformat(start_time) if start_time else '',
            'selected_end': datetime.time.isoformat(end_time) if end_time else '',
}

cache.set(cache_key, result, 60)

return result

```

- 이렇게 result에 할당을 해준다. 그리고나서 코드를 추가해서 캐시에 set이라는 명령어를 사용해서 cache_key 라는 이름으로 result를 60초의 제한시간으로 저장을 해준다. 
- 그리고 마지막에 result를 return 해준다.

- **여기까지 검색결과를 캐시로 먹이게 된 것이고, 이건 직접 우리가 코드상으로 캐시를 하는 방법을 구현한 것이다.** 데이터베이스 캐시같은 경우 지금같이 많이 작업을 한다. 

- **실제로 브라우저에서 요일과 시작시간, 종료시간을 설정하고 검색을 해보고 / redis 서버에서 다시 키를 조회하면 -> 1) ":1:\xec\x84\x9c\xec\x9a\xb8::THU:10:00:00:13:00:00:1" 이렇게 새로운 키가 있는 것을 확인할 수 있다.** 이것을 GET :1:\xec\x84\x9c\xec\x9a\xb8::THU:10:00:00:13:00:00:1 이렇게 조회해보면 캐시된 데이터를 확인할 수 있다. 
- **그래서 이 상태로 다시 검색 버튼을 누르면, 그 캐시 데이터가 그대로 오기 때문에 훨씬 빠른 속도로 검색이 되는 걸 확인할 수 있다.**





