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








### 더 고민해봐야 할 문제
- 위의 예시로 페이지 번호가 계속 늘어날 수 있지만, 늘어나는 페이지 번호를 제한하는 방법은 없을까. 예를 들어, 12345 다음에 글이 생기면 678910이 보여지게끔 하는 방법을 생각해보자.
