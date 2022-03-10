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

