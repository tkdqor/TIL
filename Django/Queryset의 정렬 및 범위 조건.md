## Queryset의 정렬 및 범위 조건 설정하기
- SQL문으로 생각해보면, SELECT 쿼리에 ORDER BY를 추가하는 것과 같다. 
- 데이터베이스 상에서 정렬 조건을 추가하지 않으면, 일관된 순서를 보장받을 수 없다. 그리고, DB에서 다수 필드에 대한 정렬을 지원하지만 -> 가급적이면 단일 필드로 하는 것이 성능에 이익이 된다.
  - 시간순/역순 정렬이 필요한 경우, id 필드를 활용해볼 수 있다.

- **정렬 조건을 지정하는 2가지 방법**
  - default 개념의 정렬이 있다. -> 모델 클래스의 Meta 속성으로 ordering 설정가능.(list로 지정) 이게 default 속성이다.
  - 그리고 원할 때, queryset에 order_by()를 지정하게 되면 위의 default가 무시되고 지정한 정렬이 사용된다.


### 모델 클래스 내부에 Meta 클래스 설정
```python
class Item(models.Model):
  name = ...
  desc = ...
  
  class Meta:
      ordering = ['id']
 ```     
      
 - 이렇게 모델 클래스 안에 클래스 구조를 설정할 수 있다. Meta 이름으로 정렬을 넣을 수 있다. 

```terminal
python manage.py shell_plus --print-spl
```

- 그리고 해당 명령어로 django shell을 실행할 수 있는데, shell_plus는 django 기본에는 없다. https://django-extensions.readthedocs.io/en/latest/installation_instructions.html 여기 django extensions 라는 라이브러리에서 shell_plus라는 명령이 지원된다. 그래서
```terminal
pip install django-extensions
```

- 해당 명령어를 통해 이 라이브러리를 설치해보자. 그리고 위의 공식문서를 보면, 그 다음에 해야 될 것들이 적혀있다.
  - 프로젝트 디렉터리 내부의 settings.py의 INSTALLED_APPS에 추가하기. 다만 app 이름은 django-extensions 이렇게 -이 포함되어 있지만, 이 자체가 package 이름이 되기 때문에 추가할 때는 'django_extensions', 이런식으로 추가하게 된다.
