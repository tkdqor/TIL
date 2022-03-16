## 카카오맵 API 사용하기



### 카카오맵 지도 API 키워드로 검색 시, 지도에 장소명이 나오게끔 하는 코드
- base.html
```html
 <!-- 검색창 -->
                <form action="{% url 'posts:hotel' %}" method="GET">
                    <div class="nav-search">
                        <input type="text" class="form-control" placeholder="가고싶은 여행지를 검색해주세요!" name="search">
                        <button class="btn btn-outline-secondary" type="submit">Button</button>
                    </div>
                </form>
```


- views.py
```python
# 숙소 페이지
def hotel(request):

    search = request.GET.get('search', '')  # HTTP GET방식으로 요청된 검색창에서 입력한 키워드를 저장

    url = 'https://dapi.kakao.com/v2/local/search/keyword.json?query={}'.format(search)   
    # url이라는 변수를 따로 정의해서 카카오 API를 저장 / 이 때 검색할 장소인 query의 값은 위의 search로 대체
    result = requests.get(url, headers={"Authorization" : "KakaoAK REST API 키 입력"})
    # 카카오 API로 HTTP Request를 GET방식으로 전송 / 헤더에 REST API 키 전달 / API 전송 결과를 result 변수에 저장
    result_dictionary = result.json()
    # API 전송 결과를 json() 형태로 바꾸고 result_dictionary 변수에 저장
    documents = result_dictionary.get('documents')
    # documents라는 key에 접근한 결과를 documents라는 변수에 저장
   
    context = {
        'search': search,
        'documents': documents,
    }

    return render(request, 'posts/hotel.html', context)
```

- hotel.html
```html
{% extends 'base.html' %}

{% load static %}

{% block style %}
{% endblock %}

{% block content %}
<div id="map" style="width:500px;height:400px;"></div>

<!-- 카카오맵 Javascript API -->
<!-- services와 clusterer, drawing 라이브러리 불러오기 -->
<script type="text/javascript" src="//dapi.kakao.com/v2/maps/sdk.js?appkey=7017ebc62c378fa5ebb089d1fd591804&libraries=services,clusterer,drawing"></script>


<!-- 지도를 띄우는 javascript 코드 작성 -->
<script>
    // 마커를 클릭하면 장소명을 표출할 인포윈도우 입니다
    var infowindow = new kakao.maps.InfoWindow({zIndex:1});
    
    var mapContainer = document.getElementById('map'), // 지도를 표시할 div 
        mapOption = {
            center: new kakao.maps.LatLng(37.566826, 126.9786567), // 지도의 중심좌표
            level: 3 // 지도의 확대 레벨
        };  
    
    // 지도를 생성합니다    
    var map = new kakao.maps.Map(mapContainer, mapOption); 
    
    // 장소 검색 객체를 생성합니다
    var ps = new kakao.maps.services.Places(); 
    
    // 키워드로 장소를 검색합니다
    ps.keywordSearch('{{ search }} + " 호텔"', placesSearchCB); 
    
    // 키워드 검색 완료 시 호출되는 콜백함수 입니다
    function placesSearchCB (data, status, pagination) {
        if (status === kakao.maps.services.Status.OK) {
    
            // 검색된 장소 위치를 기준으로 지도 범위를 재설정하기위해
            // LatLngBounds 객체에 좌표를 추가합니다
            var bounds = new kakao.maps.LatLngBounds();
    
            for (var i=0; i<data.length; i++) {
                displayMarker(data[i]);    
                bounds.extend(new kakao.maps.LatLng(data[i].y, data[i].x));
            }       
    
            // 검색된 장소 위치를 기준으로 지도 범위를 재설정합니다
            map.setBounds(bounds);
        } 
    }
    
    // 지도에 마커를 표시하는 함수입니다
    function displayMarker(place) {
        
        // 마커를 생성하고 지도에 표시합니다
        var marker = new kakao.maps.Marker({
            map: map,
            position: new kakao.maps.LatLng(place.y, place.x) 
        });
    
        // 마커에 클릭이벤트를 등록합니다
        kakao.maps.event.addListener(marker, 'click', function() {
            // 마커를 클릭하면 장소명이 인포윈도우에 표출됩니다
            infowindow.setContent('<div style="padding:5px;font-size:12px;">' + place.place_name + '</div>');
            infowindow.open(map, marker);
        });
    }
</script>

    {% for document in documents %}
    <p>{{ document.place_name }}</p>
    <p>{{ document.place_url }}</p>
    {% endfor %}



{% endblock %}
```

* * *
### 카카오맵 지도 API로 DB에 저장된 데이터의 title 필드로 검색해서 지도에 표시하기
- base.html에서 navbar
```html
<!-- navbar --> 
<nav class="navbar navbar-expand-lg navbar-light bg-light">
 ...
 <li class="nav-item">
    <a class="nav-link active" aria-current="page" href="{% url 'travels:map' %}">포토 스팟 찾기</a>
 </li>
 
</nav>
```

- views.py에서 map View
```python
# 카카오맵 API 이용해서 지도 보여주기
def map(request):


    # Travel이라는 모델 데이터베이스에 저장된 데이터 가져오기
    spots = Travel.objects.all()

    # 입력된 검색 값으로 API request 하기
    search = request.GET.get('search', '')  # HTTP GET방식으로 요청된 검색창에서 입력한 키워드를 저장
    

    url = 'https://dapi.kakao.com/v2/local/search/keyword.json?query={}'.format(search)   
    # url이라는 변수를 따로 정의해서 카카오 API URL를 저장 / 이 때 검색할 장소인 query의 값은 위의 search로 대체
    result = requests.get(url, headers={"Authorization" : "KakaoAK 537c71004ec6bb642b90b8bdf96180e5"})
    # 카카오 API로 HTTP Request를 GET방식으로 전송 / 헤더에 REST API 키 전달 / API 전송 결과를 result 변수에 저장
    result_dictionary = result.json()
    # API 전송 결과를 json() 형태로 바꾸고 result_dictionary 변수에 저장
    documents = result_dictionary.get('documents')

    context ={
        'search': search,
        'spots': spots,
        'documents': documents,
    }

    return render(request, 'travels/map.html', context)
```    

- travels/map.html 코드
```html
{% extends 'base.html' %}
{% load static %}

{% block style %}
    <link rel="stylesheet" href="{% static 'travels/map_style.css' %}">
{% endblock %}


{% block content %}
<h1>포토스팟 찾기</h1>


<!-- 검색창 -->
<form action="" method="GET">
    <div>
        <input type="text" class="form-control" placeholder="{{ search }}" name="search">
        <button class="btn btn-outline-secondary" type="submit">검색</button>
    </div>

</form>


<div class="photospot">

    <!-- 지도를 담기 위한 영역 만들기 -->
    <div id="map" style="width:500px;height:400px;"></div>


    {% for spot in spots %}
    <!-- 포토스팟 리스트 -->
    <div class="card" style="width: 18rem;">
        <img src="{{ spot.image.url }}" class="card-img-top" alt="...">
        <div class="card-body">
            <h5 class="card-title">{{ spot.title }}</h5>
            <p class="card-text">{{ spot.short_content }}</p>

            <form action="" method="GET">
                <button type="submit" name="search" value="{{ spot.title }}">위치 확인하기</button>
            </form>
        </div>
    </div>
    {% endfor %}
 
</div>    






<!-- 실제 지도를 그리는 Javascript API를 불러오기 & services와 clusterer, drawing 라이브러리 불러오기 -->
<script type="text/javascript" src="//dapi.kakao.com/v2/maps/sdk.js?appkey=7017ebc62c378fa5ebb089d1fd591804&libraries=services,clusterer,drawing"></script>


<!-- 지도를 띄우는 코드 작성 -->
<script>
    // 마커를 클릭하면 장소명을 표출할 인포윈도우 입니다
    var infowindow = new kakao.maps.InfoWindow({zIndex:1});
    
    var mapContainer = document.getElementById('map'), // 지도를 표시할 div 
        mapOption = {
            center: new kakao.maps.LatLng(37.566826, 126.9786567), // 지도의 중심좌표
            level: 3 // 지도의 확대 레벨
        };  
    
    // 지도를 생성합니다    
    var map = new kakao.maps.Map(mapContainer, mapOption); 



    // 지도에 지형정보를 표시하도록 지도타입을 추가합니다
    map.addOverlayMapTypeId(kakao.maps.MapTypeId.TERRAIN);   
    // 지도 확대 축소를 제어할 수 있는 줌 컨트롤을 생성합니다
    var zoomControl = new kakao.maps.ZoomControl();
    map.addControl(zoomControl, kakao.maps.ControlPosition.RIGHT);

    
    
    // 장소 검색 객체를 생성합니다
    var ps = new kakao.maps.services.Places(); 
    // 키워드로 장소를 검색합니다
    ps.keywordSearch('"제주도" + {{search}}', placesSearchCB); 
    // 키워드 검색 완료 시 호출되는 콜백함수 입니다
    function placesSearchCB (data, status, pagination) {
        if (status === kakao.maps.services.Status.OK) {
    
            // 검색된 장소 위치를 기준으로 지도 범위를 재설정하기위해
            // LatLngBounds 객체에 좌표를 추가합니다
            var bounds = new kakao.maps.LatLngBounds();
    
            for (var i=0; i<data.length; i++) {
                displayMarker(data[i]);    
                bounds.extend(new kakao.maps.LatLng(data[i].y, data[i].x));
            }       
    
            // 검색된 장소 위치를 기준으로 지도 범위를 재설정합니다
            map.setBounds(bounds);
        } 
    }
    


    // 지도에 마커를 표시하는 함수입니다
    function displayMarker(place) {
        // 마커를 생성하고 지도에 표시합니다
        var marker = new kakao.maps.Marker({
            map: map,
            position: new kakao.maps.LatLng(place.y, place.x) 
        });


    

        // 마커에 클릭이벤트를 등록합니다
        kakao.maps.event.addListener(marker, 'click', function() {
            // 마커를 클릭하면 장소명이 인포윈도우에 표출됩니다
            // infowindow.setContent('<div style="padding:5px;font-size:12px;">' + place.place_name + '</div>');
            // infowindow.open(map, marker);
            // 커스텀 오버레이에 표시할 컨텐츠 입니다



            // 커스텀 오버레이는 아래와 같이 사용자가 자유롭게 컨텐츠를 구성하고 이벤트를 제어할 수 있기 때문에
            // 별도의 이벤트 메소드를 제공하지 않습니다 
            var content = '<div class="wrap">' + 
                        '    <div class="info">' + 
                        '        <div class="title">' + 
                                    place.place_name + 
                        '            <div class="close" onclick="closeOverlay()" title="닫기"></div>' + 
                        '        </div>' + 
                        '        <div class="body">' + 
                        '            <div class="img">' +
                        '                <img src="place.place_url" width="73" height="70">' +
                        '           </div>' + 
                        '            <div class="desc">' + 
                        '                <div class="ellipsis">' + place.address_name + '</div>' + 
                        '                <div><a href="https://www.kakaocorp.com/main" target="_blank" class="link">자세히 보기</a></div>' + 
                        '            </div>' + 
                        '        </div>' + 
                        '    </div>' +    
                        '</div>';
            
            // 마커 위에 커스텀오버레이를 표시합니다
            // 마커를 중심으로 커스텀 오버레이를 표시하기위해 CSS를 이용해 위치를 설정했습니다
            var overlay = new kakao.maps.CustomOverlay({
                content: content,
                map: map,
                position: marker.getPosition()       
            });            


        
            // 커스텀 오버레이를 닫기 위해 호출되는 함수입니다 
            function closeOverlay() {
                var closeBtn = document.createElement("div");
                clseBtn.overlay.setMap(null);     
            }

            overlay.setMap(map);

        });

    }
</script>


{% endblock %}
``` 
* * *
### 카카오맵 API 주소로 검색해서 지도에 띄우기
```html
- spot.html 코드로 View에서 넘겨준 search와 document 변수를 사용
<!-- 포토스팟 지도로 위치 보여주기 -->
        <div id="map" style="width:800px;height:500px; margin-bottom: 50px;"></div>
        <script type="text/javascript" src="//dapi.kakao.com/v2/maps/sdk.js?appkey=자바스크립트 키 입력하기&libraries=services,clusterer,drawing"></script>
        <script>
            var container = document.getElementById('map');
            var options = {
                center: new kakao.maps.LatLng(33.450701, 126.570667),
                level: 9
            };
    
            var map = new kakao.maps.Map(container, options);

            // 주소-좌표 변환 객체를 생성합니다
            var geocoder = new kakao.maps.services.Geocoder();

            // 주소로 좌표를 검색합니다
            geocoder.addressSearch('"제주도" + {{ search }}', function(result, status) {

                // 정상적으로 검색이 완료됐으면 
                if (status === kakao.maps.services.Status.OK) {

                    var coords = new kakao.maps.LatLng(result[0].y, result[0].x);

                    // 결과값으로 받은 위치를 마커로 표시합니다
                    var marker = new kakao.maps.Marker({
                        map: map,
                        position: coords
                    });

                    // 인포윈도우로 장소에 대한 설명을 표시합니다

                    // django template language를 javascript 안에서 사용하려면 이렇게 '' 작은따옴표를 사용해보자
                    '{% for document in documents %}' 
                    var infowindow = new kakao.maps.InfoWindow({
                        content: '<div style="width:320px;text-align:center;padding:10px 0;">{{ document.address_name }}</div>'
                    });
                    infowindow.open(map, marker);
                    '{% endfor %}'
                    // 지도의 중심을 결과값으로 받은 위치로 이동시킵니다
                    map.setCenter(coords);
                } 
            });    
        </script>
```

- Views.py 코드
```python
# 포토스팟 디테일 페이지
def spot(request, travel_id):

    travel = Travel.objects.get(id=travel_id)   # 해당 travel_id 데이터를 조회해서 travel이라는 변수에 저장


    # 지도 API 기능
    search = travel.address
    url = 'https://dapi.kakao.com/v2/local/search/address.json?query={}'.format(search)
    result = requests.get(url, headers={"Authorization" : "KakaoAK REST API 키 적기"})
    result_dictionary = result.json()
    documents = result_dictionary.get('documents')


    context = {
        'search': search,
        'travel': travel,
        'documents': documents,
    }

    return render(request, 'travels/spot.html', context)
```
- Views.py에서는 DB에 저장된 Travel 모델의 주소인 travel.address를 search라는 변수에 저장하고, 해당 변수로 카카오맵 API request를 진행.



* * *
### 관련 페이지 및 블로그
- https://haries.tistory.com/6?category=885610
=> 해당 블로그에 카카오 API 관련 글들 다 보기!!

- https://apis.map.kakao.com/web/guide/
=> 카카오맵 API 기본적인 가이드

- https://developers.kakao.com/
=> 카카오 개발자 페이지 - 여기서 REST API KEY를 확인할 수 있다.

- https://developers.kakao.com/docs/latest/ko/local/dev-guide#search-by-keyword
=> 카카오 개발자 페이지에 나와있는 문서/개발 가이드 중, 키워드로 장소 검색하는 방법

- https://velog.io/@devmin/kakao-login-basic
=> django로 구현하는 소셜 로그인 기능 관련 블로그인데, 여기세 헤더로 담아서 보내는 방법이 나와있음 

- https://m.blog.naver.com/kiddwannabe/221812712712
=> 이것도 카카오맵 API 키워드 장소 검색하는 방법이 나와있는 블로그



REST API KEY가 노출되면 안되는데 github에 어떻게 올리지..?


