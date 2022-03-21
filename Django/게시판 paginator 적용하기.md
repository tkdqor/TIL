## 게시판 paginator 적용하기
- 먼저 django 공식 문서에 paginator 내용이 자세하게 나와있으니 참고하자. https://docs.djangoproject.com/ko/4.0/topics/pagination/


### 예시
- **먼저 views.py 코드**
```python
# 게시판 리스트
def index(request):
 
   posts = Post.objects.all().order_by('-id')    # Post 모델 데이터 전부를 조회해서 posts 변수에 저장

   # 페이지네이터 코드
   paginator = Paginator(posts, 3)  # Paginator를 적용할 데이터를 설정하고, 한 페이지에 몇 개의 데이터를 보여줄지 설정
   page = request.GET.get('page')   # template에서 페이지 버튼을 클릭할 때 page라는 이름의 GET 변수로 들어오기 때문에 이렇게 설정
   posts = paginator.get_page(page) # page 변수안에 페이지 번호를 받아(ex.1, 2, 3...) 전체 데이터 중 해당 페이지 보여주기

   page_range = paginator.page_range  # page_range라는 메소드를 사용해서 -> 1부터 시작하는 페이지 리스트를 반환하기 ex) range(1, 4)

   
   context = {
       'posts': posts,
       'page_range': page_range,
   }
  
   return render(request, 'posts/posts.html', context)
```

- **공식문서 참고한 template 코드**
```html
   <!-- 페이지네이터 코드 -->
   <div class="container" id="paginator_container">
    <!-- 이전 페이지 버튼 -->
    {% if posts.has_previous %}
        <a class="page_button" href="?page=1">맨 앞으로</a>
        <a class="page_button" href="?page={{ posts.previous_page_number }}">이전으로</a>
    {% else %}
        <a class="page_button" href="?page=1">맨 앞으로</a>
        <a class="page_button" href="?page=1">이전으로</a>   
    {% endif %} 

    <!-- 페이지 숫자 나타내기 -->
    {% if posts.has_previous %}
    <span>{{ posts.previous_page_number }}</span>
    {% endif %}
    <span>{{ posts.number }}</span>
    <span>|</span>
    <span>{{ posts.paginator.num_pages }}</span>

    <!-- 다음 페이지 버튼 -->
    {% if posts.has_next %}
        <a class="page_button" href="?page={{ posts.next_page_number }}">다음으로</a>
        <a class="page_button" href="?page={{ posts.paginator.num_pages }}">맨 뒤로</a>
    {% else %}
        <a class="page_button" href="?page={{ posts.paginator.num_pages }}">다음으로</a>
        <a class="page_button" href="?page={{ posts.paginator.num_pages }}">맨 뒤로</a>    
    {% endif %}    
   </div>
```

- 페이지 버튼의 href element는 GET방식으로 page라는 변수에 페이지 숫자가 들어가게끔 설정.
- view에서 설정한 posts라는 변수에 -> .has_previous / .previous_page_number / .number / .paginator.num_pages / .next_page_number 와 같은 메소드를 활용해서 pagination 적용

* * *

- **부트스트랩 paginator 이용**
```html
<!-- 페이지네이터 코드 -->
   <div>
        <nav aria-label="Page navigation example">
            <ul class="pagination">
            <!-- 이전 페이지 버튼 -->    
                {% if posts.has_previous %}    
                <li class="page-item"><a class="page-link" href="?page={{ posts.previous_page_number }}">Previous</a></li>
                {% else %}
                <li class="page-item"><a class="page-link" href="?page=1">Previous</a></li>
                {% endif %}
            
            <!-- 페이지 숫자 나타내기 -->    
                {% for i in page_range %}
                <li class="page-item"><a class="page-link" href="?page={{ i }}">{{ i }}</a></li>
                {% endfor %}
            
            <!-- 다음 페이지 버튼 -->    
                {% if posts.has_next %}
                <li class="page-item"><a class="page-link" href="?page={{ posts.next_page_number }}">Next</a></li>
                {% else %}
                <li class="page-item"><a class="page-link" href="?page={{ posts.paginator.num_pages }}">Next</a></li>
                {% endif %}
            </ul>
        </nav>
   </div>
```

- 부트스트랩을 이용해서 paginator 적용
- 위의 코드와 달리, 게시판의 글이 많아져서 페이지가 생성되면 자동으로 페이지 번호가 늘어나게끔 설정
  - {% for i in page_range %} -> range로 설정된 변수에서 for문으로 하나씩 뽑아서 i라는 변수를 페이지 번호와 href로 설정해서 자동으로 페이지 번호를 생성할 수 있음


### 더 고민해봐야 할 문제
- 위의 예시로 페이지 번호가 계속 늘어날 수 있지만, 늘어나는 페이지 번호를 제한하는 방법은 없을까. 예를 들어, 12345 다음에 글이 생기면 678910이 보여지게끔 하는 방법을 생각해보자.


### 관련 자료
- https://jupiny.com/2016/11/22/limit-pagination-page-numbers-range/
- https://docs.djangoproject.com/ko/4.0/topics/pagination/
- https://ssungkang.tistory.com/entry/Django-11-Pagination-%EC%9D%84-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90#recentComments
