## 카카오맵 API 사용하기



### 카카오맵 지도 API
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
    result = requests.get(url, headers={"Authorization" : "KakaoAK 537c71004ec6bb642b90b8bdf96180e5"})
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



