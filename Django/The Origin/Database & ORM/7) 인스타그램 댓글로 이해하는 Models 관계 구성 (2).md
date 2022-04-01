## 인스타그램 댓글로 이해하는 Models 관계 구성 (2)


### 댓글 모델 코드 작성하기
- **먼저 VSCode에서 python 인터프리터 설정을 먼저 해보자.**
- VSCode 위쪽에 보기 클릭 -> 명령 팔레트 클릭 -> 여기서 inter라고만 쳐도 Python Select Interpreter라고 나오고 이걸 클릭하자. 그 다음에는 우리가 만들었던 가상환경인 venv의 bin/python
  이걸 찾아서 클릭해주면 된다. 없으면 직접 찾아가서 들어가자. bin으로 들어가서 python.exe를 클릭해준다. 
  - **이걸 설정하는 이유는, 지금 VSCode가 어떤 python를 사용하라고 우리가 지정해준 것이다.** 그래야지 command를 누르고 클릭했을 때 이동이 된다든지, django를 제대로 인식하게 된다. 꼭 이 과정을
    진행해줘야 한다.
    
- 이제 댓글 모델을 만들어주자. models.py로 가서 설정하기
```python
from django.db import models
from django.contrib.auth import get_user_model

# get_user_model() 함수를 이용해서 User모델 생성 -> 나중에 User모델을 쉽게 커스팀하기 위함
User = get_user_model()

class Post(models.Model):
    image = models.ImageField(verbose_name='이미지')
    content = models.TextField('내용')
    created_at = models.DateTimeField('작성일')
    view_count = models.IntegerField('조회수')


class Comment(models.Model):
    content = models.TextField(verbose_name='내용')
    created_at = models.DateTimeField(verbose_name='작성일')
    post = models.ForeignKey(to='Post', on_delete=models.CASCADE)
    writer = models.ForeignKey(to=User, on_delete=models.CASCADE)    
```

- **class Comment(models.Model)**: --> **이 부분은 python은 객체지향 프로그래밍 언어이기 때문에 클래스라는 개념이 존재하게 되는데, 여기서 models.Model 이렇게 넣어주게 되면 --> Model이라는
  클래스를 상속받아서 Comment라는 클래스를 정의해주었다고 보면 된다.**
  - **해당 Model이라고 된 부분을 command로 들어가보면, class Model(metaclass=ModelBase): 이렇게 정의되어있다. 그리고 이 Model이라는 클래스 내부에 from_db 등 정말 많은 함수들이 정의되어있다. 이러한
    것들이 django에 이미 생성되어있는 것이기 때문에 우리가 이걸 가져다가 쓰면 이게 나중에 데이터베이스 테이블로 생성이 되는 일들을 사용할 수 있는 것이다.**
    
- **Comment 모델은 Post 모델과 1:N관계를 설정해야 하기 때문에, post라는 필드를 설정하고 ForeignKey를 설정해줘야 한다.** 그리고 게시글이 삭제될 때 댓글도 다 삭제될 수 있도록 CASCADE를 설정한다.
- **그리고 Comment 모델의 작성자 필드를 추가해서 User모델과도 1:N관계를 설정해주자.** 
  - **여기서는 우리가 django에 만들어져 있는 사용자 모델을 가져와야 하는데, 가장 좋은 방법은 django.contrib에 auth라는 패키지가 있는데 get_user_model이라는 함수가 있다. 그래서 이 함수로 
    User = get_user_model() --> 이렇게 User를 정의해준다.
  - 우리가 나중에 가면 User모델을 커스텀하게 될텐데, 그 때도 이 코드를 변경하지 않고 그대로 사용할 수 있는 방법이라고 보면 된다. 그래서 auth라는 인증시스템에서 정의된 get_user_model()로 모델을 가져와라라고 하는 것이고, User=get_user_model() 이렇게 모델을 생성해준다. 그리고 이 모델을 Comment모델의 writer로 설정해주는 것이다.
