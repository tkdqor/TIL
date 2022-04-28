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
        s
    
```

- **위에서보면, PostCreateForm은 바로 위에 있는 PostBaseForm를 상속받고 있는 구조이다. 그래서 내부의 Meta 클래스도 PostBaseForm의 Meta를 상속받는다.**


5:25


