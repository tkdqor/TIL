## Forms 만들기
- django에서는 유효성 검사와 데이터 처리를 위해 form를 제공하고 있다. 이 form을 사용해서 데이터를 받고 그것을 활용해보자.
- 우리가 View의 post_create_view에서는, GET방식일 경우 html을 랜더링해주고 POST 방식일 경우 이미지나 내용을 받아서 Post 모델 데이터를 생성해줬다. 이 과정에서 Form를 사용해보자.
  - 일단 기본적인 django form과 관련된 https://docs.djangoproject.com/en/4.0/ref/forms/ 공식 문서를 확인하자.

- **우리 프로젝트의 posts 앱 디렉터리 내부에 forms.py를 생성하자.**

```python
from django import forms


class PostBaseForm(forms.Form):
    image = forms.ImageField()
    content = forms.CharField()

```


- forms.py 내부에서는 바로 forms를 import 해준다. 그리고 PostBaseForm를 forms를 상속받아서 정의해준다. 일단 지금은 이 Form이라는 게 우리가 배운 모델과 비슷하다고 생각해보자.
  - 일반적으로 사용자가 데이터를 입력해서 우리한테 보내주면, 데이터를 데이터베이스에 저장하는 형태가 대부분이다. 또한, 입력받는 폼으로써 html Form가 비슷하다고 생각해도 된다. 

- Post 모델의 필드중에서 사용자에게 입력받을 때 중요한 필드는 image와 content이다. 따라서 이 2개를 forms.py에 설정해보자.
  - **필드 이름을 가지고 와서 forms까지만 입력하고 점을 입력해보면 많은 Form의 많은 필드들이 나온다. 근데 크게 Input과 / Field로 구분된다.** https://docs.djangoproject.com/en/4.0/ref/forms/fields/ 공식문서를 참고해보면, 우리가 모델 필드를 설정할 때와 비슷한 이름의 필드가 많다는 것을 알 수 있다. 
  - 그래서 image는 그냥 ImageField를 사용하면 된다. content는 TextField는 없기 때문에 CharField로 설정한다.


- **이제, 이렇게 설정한 forms.py를 우리가 View에서 가져다가 사용해보자.**

```python
from .forms import PostBaseForm

...

# forms.py를 이용해서 View 설정
def post_create_form_view(request):
    if request.method == 'GET':
        form = PostBaseForm()
        context = {
            'form': form,
        }
        return render(request, 'posts/post_form2.html', context)
    else:
        
        Post.objects.create(image=image, content=content, writer=request.user) # writer=request.user 추가 필요
        return redirect('index')


```

- 기존의 post_create_view 밑에 post_create_form_view를 새롭게 정의하자. 
- **우리가 여기에서 GET, POST 둘 다 form를 적용해야 하는데 먼저 PostBaseForm를 Import 해준다. 그리고 GET 방식일 때 form이라는 변수로 PostBaseForm 클래스를 호출한다. 그 다음 이 form 변수를 template에 넘겨주는 것이다.**
- 그리고 template은 새롭게 생성 할 post_form2.html로 설정하자.



- **그리고 URL 설정을 urls.py에서 해준다.**

```python
from .views import post_create_form_view

app_name = 'posts'

urlpatterns = [
    ...
    # Form를 사용하는 URL
    path('create/', post_create_form_view, name='post-create'),
]
```



- **여기까지 설정을 해놓고 이제 template도 수정을 해봐야 하는데, 기존의 post_form.html을 복사해서 post_form2.html를 생성해보자.**

```html
{% extends 'base.html' %}

<!-- title block -->
{% block title %}글 입력{% endblock %}

<!-- content block -->
{% block content %}
<h1>Post 입력 화면 - Form 사용</h1>
<!-- 이미지 전송을 위한 인코딩 방식 추가 -->
<form action="" method="POST" enctype="multipart/form-data"> 
    {% csrf_token %}

    <!-- forms.py 사용 -->
    <div>
        {{ form.as_p }}
    </div>
    <!-- 데이터 전송  -->
    <div>
        <input type="submit">
    </div>
</form>
{% endblock %}
```

- 그 안에 input submit 빼고는 다 지워준다. **대신에 {{ form.as_p }} 이러한 template language를 입력해주기.**
  - **그리고나서 서버를 구동시켜보면, 우리가 template에 입력하지 않아도 form이 뜨게 되고 forms.py에 정의한 필드들만 데이터를 받을 수 있게 설정해준다.**
  - **우리는 django에서 제공해주는 form이라는 클래스를 가지고서 template에 form를 랜더링할 수 있게 된 것이다.**

<img width="233" alt="image" src="https://user-images.githubusercontent.com/95380638/165698495-b662ba26-0d72-4913-8d51-79069ef6803b.png">

* * *

### django Widgets
- 이 forms.py와 관련해서 우리는 widgets를 사용할 줄 알아야 한다. 공식문서는 https://docs.djangoproject.com/en/4.0/ref/forms/widgets/ 다음과 같다. 
- 우리가 작성한 forms.py를 다시 보면,

```python
from django import forms


class PostBaseForm(forms.Form):
    CATEGORY_CHOICES = [
        ('1', '일반'),
        ('2', '계정'),
    ]

    image = forms.ImageField(label='이미지')
    content = forms.CharField(label='내용', widget=forms.Textarea, required=True)
    category = forms.ChoiceField(label='카테고리', choices=CATEGORY_CHOICES)    
```

- 여기서 content는 CharField인데, CharField에서 위젯만 바꿀 수 있다. **위젯이라는 건, html의 input 태그의 type이라고 생각하면 된다. 따라서 위젯이 변함에 따라 input의 형태가 달라진다.**
- content = forms.CharField(widget=forms.Textarea) 이렇게 수정하고 다시 브라우저를 보면, textarea로 바꿔준다.

<img width="399" alt="image" src="https://user-images.githubusercontent.com/95380638/165700142-d988135d-81c1-452c-9333-fde48df86420.png">

- **또한, category = forms.ChoiceField(choices=CATEGORY_CHOICES) 이렇게 ChoiceField를 사용해서 입력하고 Form 클래스 밑에 딕셔너리로 미리 정의를 해놓기만 해도 --> 브라우저에서 실제로 카테고리를 선택할 수 있게 화면이 바뀐다.**
  - **이렇게 우리가 django에서 form를 정의하고 --> View에서 template으로 변수를 넘겨주면 form를 쉽게 html form tag를 자유롭게 다룰 수 있게 된다.**
  - https://docs.djangoproject.com/en/4.0/ref/forms/fields/ 여기에 보면 "핵심 필드 인수" 라는 부분도 있다. content 필드에 required=True라는 속성을 주고 모든 필드에 label 속성을 주게 되면 --> 실제로 html의 label 태그가 추가된 것 처럼 앞에 label이 추가된다. required=True는 기본값으로 무조건 해당 필드에 값이 있어야 한다는 점이다. 
  - 그래서 이러한 forms.py를 사용하면 html에서 랜더링하는 것을 쉽게 도와줄 수 있다.

- **또한, django template에서 Form rendering과 관련된 여러가지 옵션들이 있다.** https://docs.djangoproject.com/en/4.0/topics/forms/#working-with-form-templates (참고)
  - 우리가 지금까지 사용했던 건, {{ form.as_p }}로 \<p\> 태그를 사용해서 form안에 들어가서 쓰는 것이다. 그런데 이것 말고도 위의 공식문서를 보면, 다양한 옵션이 있다. 
  - {{ form.as_ul }}은 form안에 li 태그로 들어가게 된다. 또는 {{ form.as_table }}를 사용하면 table로 들어가게 된다. 

- **또는, form를 for문을 사용해서 반복적으로 쓸 수도 있다.** post_form2.html에서,

```html
...
<form action="" method="POST" enctype="multipart/form-data"> 
    {% csrf_token %}

    <!-- forms.py 사용 -->
    <div>
        <!-- {{ form.as_table }} -->
        {% for field in form %}
            {{ field.label }}
            {{ field }}
        {% endfor %}
    </div>
    <!-- 데이터 전송  -->
    <div>
        <input type="submit">
    </div>
</form>
{% endblock %}
```

- 이런식으로 {% for field in form %} 필드를 하나씩 빼서 필드 라벨과 자체를 출력할 수 있다. 

* * *

### 실제로 데이터 입력 시, 사용할 수 있도록 View 설정하기
- 다시 views.py에서 post_create_form_view를 수정해서 데이터가 실제로 입력되었을 때 받을 수 있도록 해보기. if문으로 GET이 아닐 때,

```python
# forms.py를 이용해서 View 설정
def post_create_form_view(request):
    if request.method == 'GET':
        form = PostBaseForm()
        context = {
            'form': form,
        }
        return render(request, 'posts/post_form2.html', context)
    else:
        form = PostBaseForm(request.POST, request.FILES)

        # form 자체에 대한 유효성 검사
        if form.is_valid():
            Post.objects.create(image=form.cleaned_data['image'], content=form.cleaned_data['content'], writer=request.user) 
        else:
            return redirect('posts:post-create')
        
        return redirect('index')
```

- **POST방식일 때는, form = PostBaseForm(request.POST) 이렇게 request의 POST라고 해줘야 한다.**
  - form = PostBaseForm()처럼 --> 양식을 제공하기 위해서는 인자에다가 아무것도 넣지 않으면 GET 방식으로 양식을 제공해준다. 
  - 반면 POST로 데이터를 받았을 때는 form = PostBaseForm(request.POST) 이렇게 값을 넣어준다. 그러면 사용자가 입력한 content 내용을 받을 수 있다. (반면 image는 POST가 아니라 FILES로 가져오는 것이기 때문에 request에는 없다.) 
  - **이미지를 받으려면 form = PostBaseForm(request.POST, request.FILES) 이렇게 추가해주면 된다.**

- **이렇게 가져온 데이터를 사용해서 Post 모델 데이터에 추가하려면, Post.objects.create(image=form.cleaned_data['image'], content=form.cleaned_data['content'], writer=request.user) --> 이런식으로 cleaned_data를 사용해서 값을 이용할 수 있다.**
  - cleaned_data란, Form에서는 랜더링을 제공해줄 뿐만 아니라 유효성 검사도 해주고 제대로 수행되었을 때도 처리를 해줄 수 있다. 그래서 cleaned_data는 유효성 검사가 완료되었다는 의미로 사용한다. 그래서 예를 들어, integer인데 파일이 들어오면 잘못된 것이고 image인데 그냥 text만 들어와도 잘못된 것이다. 이러한 유효성 검사들을 우리가 작성한 forms.py를 기반으로 해서 django가 대신 해주는 것이다.
  - **그런데, 우리가 forms.py에서 cleaned_data를 사용하려면 --> forms.py에서 작성한 form이 유효한지를 판단하는 함수를 먼저 호출해줘야 한다.** if form.is_valid(): 이렇게 호출하면 되는데, is_valid라는 함수는 우리가 받은 필드들이 유효성 검사가 적합한지를 판단해서, 적합하다면 True 아니면 False를 반환해준다. 그래서 위의 코드에서는 적합하다면 Post 모델 데이터를 저장해준다.

- **이렇게 forms.py를 작성해서 우리가 직접 유효성 검사를 하지 않아도, django Form에서 대신해서 하게 해준다. 그래서 우리가 django Form을 쓸 때는 크게 2가지 기능을 위해 사용한다고 보면 된다.**
  - 우리가 html에서 form를 랜더링시키기 위한 form 태그를 만들어주는 역할 
  - 우리가 사용자한테 데이터를 받을 때, 해당 데이터의 유효성 검사를 해주는 역할









