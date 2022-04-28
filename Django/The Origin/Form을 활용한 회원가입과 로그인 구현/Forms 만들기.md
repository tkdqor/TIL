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
        return render(request, 'posts/post_form.html', context)
    else:
        
        Post.objects.create(image=image, content=content, writer=request.user) # writer=request.user 추가 필요
        return redirect('index')


```

- 기존의 post_create_view 밑에 post_create_form_view를 새롭게 정의하자. 
- **우리가 여기에서 GET, POST 둘 다 form를 적용해야 하는데 먼저 PostBaseForm를 Import 해준다. 그리고 GET 방식일 때 form이라는 변수로 PostBaseForm 클래스를 호출한다. 그 다음 이 form 변수를 template에 넘겨주는 것이다.**

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

- **여기까지 설정을 해놓고 이제 post_form.html도 수정을 해보자.**









