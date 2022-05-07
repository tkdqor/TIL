## django 어드민
- django에서는 기본적으로 데이터베이스의 데이터를 CRUD할 수 있는 웹페이지를 제공한다. 그게 바로 django 어드민이다. 
- 기존에 어드민 아이디가 있다면 패스워드를 변경할 수도 있다.

```terminal
python manage.py changepassword admin
```

- 그래서 만약 비밀번호를 잊어버렸다면 이렇게 바꿔주자.

- 그리고 urls.py로 가보면, 장고 어드민 페이지들을 볼 수 있게 설정해준다. admin자체에 모듈들이 있고, 여기서 urls를 command로 클릭 - get_urls를 클릭하면 path가 정의되어있는 것을 확인할 수 있다. django 어드민에서 제공하는 모든 페이지들의 경로 규칙들이 담겨져 있다고보면 된다. 


### django 어드민 페이지 살펴보기
- 이제 서버를 구동시키고 admin 페이지를 들어가보자. 그러면 모델들이 나와있고 직접 데이터를 수정할 수 있다. 
- 하지만 개발자 입장에서는, DBeaver와 같은 DB 툴을 사용하는게 조금 더 편할 수 있다. 


### django 어드민 페이지 모델 추가하기
- web 앱에있는 admin.py를 들어가보자. 여기에 추가하길 원하는 모델을 추가할 수 있다. 먼저 restaurant 모델을 추가해보자.

```python
from django.contrib import admin
from .models import Restaurant

# Register your models here.
class RestaurantAdmin(admin.ModelAdmin):
    fields = ['name', 'address']

admin.site.register(Restaurant, RestaurantAdmin)
    
```

- 이런식으로 ModelAdmin이라는 클래스를 상속받아서 클래스를 만들면 모델 하나를 매핑해서 어드민페이지에서 띄워준다. 그리고 register에 모델과 모델 어드민을 등록해주면 페이지에 뜬다.
  fields는 인스턴스 하나 클릭할 때 띄워주는 필드이다. 그 안에 category도 설정해주자. 근데 지금은 그냥 pk로만 뜨게 되는데, 이걸 우리가 알아보기 쉽게 해줄 수 있다. 
  
- **우리가 models.py에서 모델 클래스마다 그 클래스의 객체, 그 클래스를 대표하는 명칭을 출력하는 함수를 사용할 수 있다.**
  - python에서 기본적으로 표준처럼 쓰는데, 클래스 내부에서 def 다음에 언더바 2개를 입력해보면 사전에 정의되어있는 함수가 여러 개 있다. 이런것들을 통해서 클래스의 정보들을 python에서 얻어올 수 있다. 그 중에서 "def __str__" 라는 문자열로 변환하는 함수가 있다.  

```python
...

class Category(models.Model):
    name = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now_add=True, null=False)

    def __str__(self):
        return self.name
```

- **해당 클래스의 정체가 무엇인지 문자열로 출력해주는 역할을 한다. 자바에서도 비슷하게 to_string이라는 메소드가 클래스마다 있다. 이게 기본적으로 모델이름과 (pk)이렇게 출력되게 설정되어있는 것이다.**
- 그래서 이걸 name을 출력하도록 수정해보자. 
- 그러면 이제 한글로 카테고리가 표시되는 것을 확인할 수 있다. 

<img width="720" alt="image" src="https://user-images.githubusercontent.com/95380638/167235672-f1725656-4bb6-432b-9bef-42e71600eac6.png">

- 위에 화면 
