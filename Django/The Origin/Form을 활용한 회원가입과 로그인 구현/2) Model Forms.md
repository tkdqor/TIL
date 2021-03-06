## Model Forms
- 지난 강의에서 배운 Form를 더 자세하게 알아보기. forms.py로 다시 돌아가자.
- **기존에는 class PostBaseForm(forms.Form): 이렇게 forms.Form 을 상속받았는데, 이번에는 Model Form를 사용해보자.**

```python
from django import forms
from .models import Post


class PostBaseForm(forms.Form):
    CATEGORY_CHOICES = [
        ('1', '일반'),
        ('2', '계정'),
    ]

    image = forms.ImageField(label='이미지')
    content = forms.CharField(label='내용', widget=forms.Textarea, required=True)
    category = forms.ChoiceField(label='카테고리', choices=CATEGORY_CHOICES)    


class PostCreateForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = '__all__'    
```


- **이렇게 ModelForm를 상속받은 PostCreateForm를 정의해준다. 이렇게 ModelForm를 상속받을 때는 클래스 내부에 Meta 클래스를 하나 더 정의해줘야 한다.**
  - 그리고 우리가 사용하고자 하는 모델을 입력해주고 fields도 정의해준다.


- **그 다음, views.py로 돌아가서 post_create_form_view에서 GET방식일 때 -> form 변수를 PostCreateForm으로 바꿔줘보자.**

```python
from .forms import PostBaseForm, PostCreateForm
...

# forms.py를 이용해서 View 설정
def post_create_form_view(request):
    if request.method == 'GET':
        form = PostCreateForm()
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

- **이렇게 GET 방식일 때 form 변수를 PostCreateForm()으로 바꿔주고 브라우저를 보면, Post 모델의 모든 필드들을 입력할 수 있도록 출력해준다.**
  - 우리가 forms.py에서 단 4줄만 작성했는데 이렇게 나올 수 있는 이유는, 대부분은 폼에서 요청을 날렸을 때 모델을 저장을 한다. 그래서 모델을 보면 Post 모델의 모든 필드가 PostCreateForm 여기에 알아서 들어가게 된 것이다. 우리가 ModelForm를 상속을 받았고, Meta 클래스에다가 모델을 Post로 지정해주었기 떄문에, 그리고 필드는 전체 필드를 다 가져오라고 지정을 해주었기 때문에 이렇게 출력을 할 수 있는 것이다. 
  - **그래서 class PostBaseForm(forms.Form): 이렇게 작성을 할 수도 있지만 / class PostCreateForm(forms.ModelForm): 이렇게 ModelForm을 이용해서 더 간편하게 작성할 수도 있다.**

* * *

- **우리가 실질적으로 작성하는 구조처럼 하기 위해서 기존의 forms.py에 있었던 코드들을 전부 수정해주자. (기존 코드는 주석처리)**

```python
class PostBaseForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = '__all__'


class PostCreateForm(PostBaseForm):
    class Meta(PostBaseForm.Meta):
        fields = ['image', 'content']


class PostUpdateForm(PostBaseForm):
    class Meta(PostBaseForm.Meta):
        fields = ['image', 'content']


class PostDetailForm(PostBaseForm):
    def __init__(self, *args, **kwargs):
        super(PostDetailForm, self).__init__(*args, **kwargs)
        for key in self.fields:
            self.fields[key].widget.attrs['disabled'] = True
    
```

- **위에서보면, PostCreateForm은 바로 위에 있는 PostBaseForm를 상속받고 있는 구조이다. 그래서 내부의 Meta 클래스도 PostBaseForm의 Meta를 상속받는다.**
  - 그리고 본인이 원하는 필드만 설정해줄 수 있다. 

- **그래서 이렇게 하나의 BaseForm를 정의해서 그걸 상속받아 다양하게 활용할 수 있다.**
  - PostDetailForm를 예를 들면, View의 post_detail_view에서 content로 form를 넘겨주고, template에서 {{ form.as_p }} 이렇게 사용하면 폼이 띄워진다. 

* * *

### django template language에서도 주석 처리가 있다. 

```html
{# {{ form.as_p }} #}
```

- **기존의 html 방식으로 <! -- ... -- > 이런식으로 주석처리를 하게 되었을 때는 크롬의 개발자 도구로 봤을 때 코드가 나와있는 걸 확인할 수 있는데, 위의 방식으로 주석처리를 하게 되면 코드가 아예 나오지 않는다. html 방식은 일단 서버에서 django template 언어를 가져오고 그 다음에 html쪽에서 주석 처리를 하는 거니까 코드가 나와있게 된다.**

- **결론적으로, forms.py같은 경우에는 쉽게 html에서 랜더링하는 것을 도와주고 클라이언트가 데이터를 요청한다면, 그 요청에 대한 유효성 검사를 해서 그 유효성 검사가 통과된 데이터들을 저장할 수 있다는 장점이 있다.**
  - 이 forms.py도 커스텀을 할 수 있다. 관련 공식 문서 내용은 https://docs.djangoproject.com/en/4.0/ref/forms/validation/ 다음과 같다. 다시 forms.py로 가서,

```python
from django.core.exceptions import ValidationError
...
class PostCreateForm(PostBaseForm):
    class Meta(PostBaseForm.Meta):
        fields = ['image', 'content']
    
    def clean_content(self):
        data = self.cleaned_data['content']
        if "비속어" == data:
            raise ValidationError("'비속어'는 사용하실 수 없습니다.")

        # Always return a value to use as the new cleaned data, even if
        # this method didn't change it.
        return data
        
```

- **다음과 같이 PostCreateForm이 있을 때, "clean_필드명" 이라는 메소드를 정의해주는 것이다. 데이터를 받을 때, 만약 비속어가 있다면 import 해놓은 ValidationError를 발생시킨다. 따라서 비속어라는 단어가 있으면 유효성 검사가 통과되지 못하게 한다.** 
- 이렇게 해놓고 실제로 글 1개를 생성할 때, 내용에 비속어 라고 입력하고 생성하면 저장이 안 되는 모습을 볼 수 있다.
- **위의 코드에서는 def clean_content 이렇게 함수명을 정했는데 --> 유효성 검사에 대한 커스텀을 할 때, clean_ 하고나서 그 뒤에다가 필드명을 content처럼 붙이게 되면 자동으로 content 필드에 대한 유효성 검사 함수가 되는 것이다.**
  - **지금은 한 필드에 대해서 커스텀을 한 것이지만, def clean(self): 이렇게 전체에 대한 커스텀도 진행해볼 수 있다. 공식문서를 참고해보자.**


- **강의 21분 --> 디버깅 기능 추가해서 다시보기**












