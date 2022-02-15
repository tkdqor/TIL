## CRUD 웹페이지 만들기
- 먼저, template를 table element를 활용해서 구성해보기.
  - table 관련 MDN 사이트 참고 : https://developer.mozilla.org/ko/docs/Web/HTML/Element/table

- posts 앱 내부에 있는 templates -> posts -> index.html파일 열기. 지금은 ul, li element로 구성되어 있으니 table로 표현해보자.

```html
<h1>Posts</h1>

    <table>
        <tr>
          <td>John</td>
          <td>Doe</td>
        </tr>
        <tr>
          <td>Jane</td>
          <td>Doe</td>
        </tr>
    </table>
```

- 이렇게 table element를 추가해보자. 그러면 간단한 table이 생성된 것을 확인할 수 있다.

```css
table, td {
    border: 1px solid black;
}
```

- table에 대한 css도 위와 같이 설정한다. -> 모든 table element와 td element에 대해서 1px로 실선, 검은색 테두리를 주었다. 
- command + + 또는 - 로 웹페이지 비율을 조정해보면 테두리가 2줄이 생긴다. 셀에도 테두리가 있고, 전체 테이블 테두리 외곽에도 있다. 

```css
table, td {
    border: 1px solid black;
    border-collapse: collapse;
}
```

- 이렇게 border-collapse라는 property를 추가해주면 테이블이 실선 하나의 테두리로 바뀌게 된다. 사실은 collapse가 되서 중첩된 것이다.


### Table element
- table를 만들고 싶으면 먼저, **table element**를 작성한다. 
  - 그리고나서 **tr, table row는 각 행을 의미한다.** 행 한 줄을 작성하겠다고 한다면 tr element를 하나 추가해주면 된다. 테이블의 행이 몇 줄인지 파악할 수 있게 해준다.
  - 각 줄을 세로로 쪼개서 칸을 만들어줘야 한다. 그래서 tr element 내부에다가 -> **td element, 즉 table data의 약자로 한 row를 몇 개의 셀로 구성할지를 결정할 수 있다.**

- 우리가 일반적으로 table를 사용할 때는, 맨 위에 있는 행에다가 -> 어떠한 데이터가 아래에 쌓일지 그 데이터 자체를 설명하는 column의 제목, 필드의 제목을 정의한 다음에 하단에는 관련 데이터가 쌓이게 된다.
  - **그래서 이러한 부분을 명시적으로 표현하기 위해서 table의 첫번째 행에 해당하는 부분을 우리는 table의 header(<thead>)라고 부른다.** 
  - **그리고 이 table header 아래에 실제 데이터가 row by row로 들어갈 부분을 table body(<tbody>)라고 부른다.**
    
 ```html
  <h1>Posts</h1>

    <table>
        <thead>
            <tr>
                <th>First name</th>
                <th>Last name</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>John</td>
                <td>Doe</td>
              </tr>
              <tr>
                <td>Jane</td>
                <td>Doe</td>
              </tr>
        </tbody>      
    </table>
```  

    
<img width="167" alt="image" src="https://user-images.githubusercontent.com/95380638/153920726-7bbd138f-3025-49ab-bf48-20a5892f8c4d.png">
    
- 다음과 같은 table을 구성할 수 있다.
- 그리고 thead에 있는 element애서 td가 아닌, th 라는 element를 사용하게 되면, header를 위한 셀로 변경된다. 
  - th element가 사용된 부분을 보게 되면, 글씨가 조금 더 진하게 표시된 것을 확인할 수 있다.

    
### index.html 수정하기
```html
<body>
    <h1>Posts</h1>

    
    {% if posts %}
     <table>
         <thead>
            <tr>
                <th>#</th>
                <th>작성자</th>
                <th>본문</th>
                <th>게시일</th>
            </tr>
         </thead>
         <tbody>
            {% for post in posts %}
                <tr>
                    <td>{{ post.id }}</td>
                    <td>{{ post.author }}</td>
                    <td>{{ post.body }}</td>
                    <td>{{ post.created_at }}</td>
                </tr>
            {% endfor %}
         </tbody>
     </table>
     {% else %}
        <p>No Post</p>
    {% endif %} 
        
</body>    
```
    
- for문을 통해 반복적으로 tbody 안에 있는 tr element를 출력할 수 있게끔 해주기.

- 여기까지 여러 개의 게시물을 한꺼번에 조회(Read)할 수 있는 웹페이지가 완성. 
    
* * *
    
### 게시물 상세 조회 페이지
- 게시물 1개의 상세 조회 페이지를 만들기 위해, 테이블에 column 1개를 더 만들고 링크 추가하기.
```html
<body>
    <h1>Posts</h1>

    
    {% if posts %}
     <table>
         <thead>
            <tr>
                <th>#</th>
                <th>작성자</th>
                <th>본문</th>
                <th>게시일</th>
                <th>상세</th>
            </tr>
         </thead>
         <tbody>
            {% for post in posts %}
                <tr>
                    <td>{{ post.id }}</td>
                    <td>{{ post.author }}</td>
                    <td>{{ post.body }}</td>
                    <td>{{ post.created_at }}</td>
                    <td><a href="/posts/{{ post.id }}">보기</a></td>
                </tr>
            {% endfor %}
         </tbody>
     </table>
     {% else %}
        <p>No Post</p>
    {% endif %} 
        
</body>
```
    
- table column를 1개 더 만들고, 보기라는 a element를 추가해서 href에 /posts/{{ post.id }} 이렇게 게시글 상세 페이지마다 url를 설정.
  - 그래서 만약 1번 게시물을 누르면 -> /posts/1 이라는 url로 이동되는 것을 확인할 수 있다.
    
    
- 이제, 위의 url에 해당하는 urls.py와 View 함수도 수정해보자.
```python
from django.urls import path
from . import views


urlpatterns = [
    path('', views.index),
    path('<int:pk>/', views.detail),
]
```
    
- 위와 같이 /posts/1 이러한 숫자를 받을 수 있는 urls.py를 설정
    
```python
# 게시판 상세 페이지
def detail(request, pk):
    post = Post.objects.get(id=pk)
    
    context = {
        'post': post,
    }

    return render(request, 'posts/detail.html', context)
```
    
- views.py에서는 detail 함수를 설정하고, url에서 보내준 Post 모델의 pk 값으로 조회해서 단 건의 데이터를 post라는 변수에 저장한다. 그리고 detail.html에 보내준다.    
- 앱 내부에 있는 templates -> posts -> 여기에 detail.html 파일을 새롭게 생성해주자.    

```html
<body>
    <h1>Post detail</h1>

    
    {% if post %}
        <h2>작성자</h2>
        <p>{{ post.author }}</p>
        
        <h2>본문</h2>
        <p>{{ post.body }}</p>

        <h2>게시일</h2>
        <p>{{ post.created_at }}</p>
    {% else %}
        <p>No Post</p>
    {% endif %} 
  
    <a href="/posts/">목록</a>
        
</body>
```
    
- View detail 함수에서 post라는 이름의 변수를 전달해줬기 때문에, detail.html에서도 post라는 변수를 사용해서 Post 모델 1개의 객체에 대한 데이터를 출력해줄 수 있다.
    

* * * 
- **여기까지 조회하는 부분에 대한 모든 웹페이지를 구현했다.**
  - 페이지와 페이지간의 연결을 위해 a element를 사용했는데 그 a element의 href 값에 대해 우리가 전부 하드코딩을 진행했다.
  - 이렇게 직접 하드코딩 하게되면, 오타가 날 수 있고 url pattern를 바꾸게 되면 하나하나 다 수정해줘야 한다.

- **그래서, urls.py에서 각 url에 대한 이름을 붙여 사용할 수도 있다. 각 url pattern에 대해서 별칭을 부여하여 사용하는 방식을 url pattern naming이라고 한다.**
  - **또한, 부여한 이름도 다른 App과 겹칠 수 있기 때문에 -> App 이름을 앞에 붙여서 구분할 수 있도록 사용할 수 있다. 이를 위해 App의 urls.py에다가 app_name이라는 변수로 app의 이름을 저장하면 된다.**
  - 그리고나서 template에서는 django template langauge로 url tag를 사용하여 **{% url 'posts:index' %}** 이렇게 posts 앱의 index 이름을 가진 url pattern를 사용하겠다고 지정할 수 있다.
    - posts 앱의 detail url pattern의 경우에는 -> 해당 Post 모델의 pk값이 반드시 필요한 url이니까 **{% url 'posts:detail' post.id %}** 이렇게 post.id를 같이 전달해줘야 한다.
    
```python
from django.urls import path
from . import views


app_name = 'posts'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:pk>/', views.detail, name='detail'),
]

```    
    
- posts 앱 내부 urls.py에서 app_name을 지정해주고, 앱이름을 값으로 설정해주자.
- 그리고, path 함수의 세번째 인자로 name을 사용하여 별칭을 지정하자.    
    
    
    
```html
<tbody>
   {% for post in posts %}
      <tr>
         <td>{{ post.id }}</td>
         <td>{{ post.author }}</td>
         <td>{{ post.body }}</td>
         <td>{{ post.created_at }}</td>
         <td><a href="{% url 'posts:detail' post.id %}">보기</a></td>
      </tr>
   {% endfor %}
</tbody>
```   
    
    
- html 파일에서도 a element에 기록한 url을 더 편하게 설정할 수 있다. 
    
- 여기까지 CRUD 중, Read에 대한 내용이다.
