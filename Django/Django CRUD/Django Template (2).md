## django Template 데이터 활용하기
- view 함수에서 render 함수의 세번째 인자로 HTML에서 표현하고자 하는 데이터를 담고있는 딕셔너리인 context를 전달하면, 해당 데이터가 template으로 전달되어 데이터를 사용할 수 있게 된다.
  - 딕셔너리 내부의 'key'가 변수로 활용된다.
- 이 변수를 html 내부에서 출력하고자 한다면, 중괄호를 2번 겹쳐서 그 안에 변수를 넣어주면 된다.

```python
def index(request):
    context = {'post': '나의 첫 django project...'}
    return render(request, 'posts/index.html', context)
```

```python
 <ul>
      <li>{{ post }}</li>
 </ul>
```

- 이렇게 views.py와 html 파일 설정을 할 수 있다.

- 우리가 django template language를 기반으로 template를 작성하면, **django template engine**이라는 것이 이 template 내부의 코드를 계산해서 최종적으로는 순수한 HTML 코드로 변환시켜준다.
  - django template engine이 {{ post }} 라는 코드를 읽으면 context 내부에서 post라는 데이터를 찾아서 value값을 활용하여 HTML 코드로 변환시키는 것이다.

- 이렇게 django template를 만들어 두고, 사용자의 데이터만 전달해주는 것으로 서로 다른 웹 페이지를 보여줄 수 있다.
  - context 딕셔너리 내부에 또다른 딕셔너리, 리스트, 클래스의 인스턴스 등 다양한 데이터를 전달할 수 있다. 해당 데이터를 template에 전달하고 / template에서는 모두 다 점(.)으로 접근하게 된다.


### 예시
```python
class Member:
    def __init__(self, name, age):
        self.name = name
        self.age = age


def index(request):

    context = {
        'post': {
            'author': 'sangbaek',
            'body': '#django Project...'
        },    
        'numbers': [10, 20, 30],
        'member': Member('sangbaek', 29),
    }
    return render(request, 'posts/index.html', context)
```    

- 위와 같이 views.py에서 Member라는 클래스를 정의하고, index 함수에서는 딕셔너리와 리스트, 클래스 인스턴스를 담고있는 context 딕셔너리를 설정할 수 있다. 그 다음은 html 파일에서 해당 데이터에 접근해보자.

```html
<body>
    <h1>Post</h1>
    <ul>
        <li>Author: {{ post.author }}</li>
        <li>Body: {{ post.body }}</li>
    </ul>

    <h2>Numbers</h2>
    <ul>
        <li>{{ numbers.0 }}</li>
        <li>{{ numbers.1 }}</li>
        <li>{{ numbers.2 }}</li>
    </ul>

    <h2>Member</h2>
    <ul>
        <li>{{ member.name }}</li>
        <li>{{ member.age }}</li>
    </ul>
</body>
```

- context가 보내준 데이터의 key 값을 변수로 활용하게 되고, 그 변수에 해당하는 value값을 활용하기 위해 점(.)을 사용하고 있음을 확인할 수 있다. 이렇게 설정한 다음 html 파일을 보면 데이터가 채워져 있다.


### 예시 2
- django template language에서 **{% if post %}** 와 같이 %를 활용하는 경우는, 실제로 HTML 화면에 출력하고자 하는 목적이 아니라 특수한 기능을 위해 사용하는 경우라고 볼 수 있다. ex). {% 해당기능 %}

```python
def index(request):

    context = {
        'posts': [
            {'author': 'sangbaek', 'body': 'django project 1'},
            {'author': 'sangbaek', 'body': 'django project 2'},
            {'author': 'sangbaek', 'body': 'django project 3'},
            {'author': 'sangbaek', 'body': 'django project 4'},
        ]
    }
    return render(request, 'posts/index.html', context)
```

- 이와 같이 context 딕셔너리안에, posts라는 key에 대응해서 -> 딕셔너리가 여러 개 있는 리스트를 value값으로 설정할 수 있다. 이러한 context를 html에 넘겨주면, posts라는 변수를 활용해서 데이터를 표시할 수 있다.

```html
<body>
    <h1>Posts</h1>
    <ul>
        <li>Author: {{ posts.0.author }} | body: {{ posts.0.body }}</li>
        <li>Author: {{ posts.1.author }} | body: {{ posts.1.body }}</li>
        <li>Author: {{ posts.2.author }} | body: {{ posts.2.body }}</li>
        <li>Author: {{ posts.3.author }} | body: {{ posts.3.body }}</li>
    </ul>
</body>
```

- 물론 이렇게 li element를 개수만큼 다 적을 수도 있지만, 나중에는 점점 더 많아질 것이다. for문을 사용해서 다시 입력해보면, 


```python
<body>
    <h1>Posts</h1>
    
    {% if posts %}
        <ul>
            {% for post in posts %}
                <li>Author: {{ post.author }} | body: {{ post.body }}</li>
            {% endfor %}
        </ul>        
    {% else %}
            <p>게시글이 없습니다!</p>
    {% endif %}
</body>
```

- 위와 같이, 전체 if문을 설정하고 만약 posts라는 변수가 있으면 -> for문을 이용해서 li element를 만들어주고 / 없으면 다른 글을 보여줄 수 있도록 설정할 수 있다.
