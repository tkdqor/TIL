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

- **위에 화면에서 카테고리는 바뀌었지만, restaurant 모델의 필드들도 설정할 수 있다.**

```python
...

class Restaurant(models.Model):
    name = models.CharField(max_length=200, db_index=True, verbose_name=_('이름'))
    category = models.ForeignKey(Category, on_delete=models.CASCADE, default=1, verbose_name=_('카테고리'))
    main_image = models.ForeignKey('RestaurantImage', related_name='main_image', null=True, on_delete=models.CASCADE, verbose_name=_('메인 이미지'))
    address = models.CharField(max_length=300, db_index=True, verbose_name=_('주소'))
    phone = models.CharField(max_length=20, verbose_name=_('연락처'))
    visible = models.BooleanField(default=True, verbose_name=_('표시 여부'))
    latitude = models.FloatField(null=True, default=None, verbose_name=_('위도'))
    longitude = models.FloatField(null=True, default=None, verbose_name=_('경도'))
    created_at = models.DateTimeField(auto_now_add=True, null=False, verbose_name=_('생성일시'))
    updated_at = models.DateTimeField(auto_now=True, verbose_name=_('수정일시'))
    menu_info = models.TextField(null=True, verbose_name=_('메뉴 정보'))
    description = models.TextField(null=True, verbose_name=_('설명'))
```

- **이렇게 models.py에서 모델을 정의할 때, 필드 속성에다가 verbose_name이라는 것을 선언해주면 된다. 여기서도 gettext_lazy를 사용해서 나중에 번역할 때를 대비해서 선언해주면 나중에 좋을 것이다.**
- 위에 처럼 name필드를 수정해보면 실제로 '이름'이라고 바뀐다. 그래서 위와 같이 Restaurant 모델의 필드들에 전부 verbose_name이라는 것을 선언해주면 어드민 페이지에서 확인할 때 모든 필드들이 해당 글로 뜨게 된다. 
- **그리고 기본적으로, 모델을 수정했으니까 migration과 migrate도 해줘야 한다.**


### 어드민 페이지 모델 클릭 시, 리스트 화면 수정하기
- 지금은 restaurant 모델을 클릭하면 그냥 Restaurant object (23) 이렇게 pk값만 나오게 된다. 
- 이걸 수정하기 위해서는, admin.py에서 수정해주면 된다.

```python
from django.contrib import admin
from .models import Restaurant

# Register your models here.
class RestaurantAdmin(admin.ModelAdmin):
    list_display = ('id', 'name', 'category', 'address')
    fieldsets = [
        ('주요정보', {'fields': ['name', 'category', 'address', 'phone', 'visible']}),
        ('상세정보', {'fields': ['menu_info', 'description']})
    ]

admin.site.register(Restaurant, RestaurantAdmin) 

```

- **여기서 list_display라는 필드를 설정해주면 된다.**
  - 참고로 이러한 필드가 어떤것이 있는지 알고 싶다면, **ModelAdmin를 command로 클릭**해보면 해당 클래스 내부에 우리가 사용해볼 수 있는 변수들이 나와있다. 

- **list_display는 튜플로 들어가게 된다. 여기서 리스트에 보여지길 원하는 필드들을 나열하면 된다.**

- **또한, 그냥 fields가 아니라 fieldsets라는 것을 사용해볼 수도 있다.**
  - 리스트 내에 튜플로 설정해서 여러 필드들을 묶음으로 처리할 수 있다. 그 안에 어떤 필드들을 묶을지는 딕셔너리로 표현해준다.
  - **fieldsets은 ModelAdmin에서도 이미 정의되어있는 건데, 여기에 어떤 값을 리스트로 넣어주게 된다. 그리고 여기 안에 들어가있는 튜플의 개수만큼 그룹을 지어준다. 여러 필드들을 한 묶음으로 화면에 표시하도록 그룹을 지어준다. 그 그룹의 이름을 처음에 튜플의 0번째 항목으로 넣어주는 것이다. 그 다음 튜플의 1번째 항목으로 속성들을 딕셔너리로 넣어준다. 여기서 키인 fields는 어떤 필드들을 묶을 것인지 정의하는 것이고 값에 리스트를 넣어주는 것이다.** 

<img width="1110" alt="image" src="https://user-images.githubusercontent.com/95380638/167236627-d990050b-840e-4070-af30-4167dfd470ae.png">

<img width="773" alt="image" src="https://user-images.githubusercontent.com/95380638/167236635-141929a4-29d2-43a8-a983-6e876a996f45.png">

- 위와 같이 설정하고 다시 어드민 페이지를 보면 이렇게 리스트에서는 우리가 정의한 필드들이 출력된다. 그리고 pk를 눌러서 들어가게 되면 "주요정보"와 "상세정보"로 묶음이 되어서 정보를 나눠서 보여준다.





