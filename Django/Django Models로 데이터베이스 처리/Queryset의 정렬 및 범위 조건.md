## Queryset의 정렬 및 범위 조건 설정하기
- SQL문으로 생각해보면, SELECT 쿼리에 ORDER BY를 추가하는 것과 같다. 
- **데이터베이스 상에서 정렬 조건을 추가하지 않으면, 일관된 순서를 보장받을 수 없다. 그리고, DB에서 다수 필드에 대한 정렬을 지원하지만 -> 가급적이면 단일 필드로 하는 것이 성능에 이익이 된다.**
  - 시간순/역순 정렬이 필요한 경우, id 필드를 활용해볼 수 있다.


## 정렬 조건을 지정하는 2가지 방법
- default 개념의 정렬이 있다. -> **모델 클래스의 Meta 속성으로 ordering 설정가능.(list의 형태로 지정하면 된다.) 이게 default 속성이다.**
  - **Meta 클래스는, 모델 자체에 대한 다양한 사항을 정의하는데 사용할 수 있다. 권한이나 순서 지정, 데이터베이스 이름 등 다양한 설정을 할 수 있다.**
  - 관련 내용 https://www.delftstack.com/ko/howto/django/class-meta-in-django/ 


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


### django extensions 설치하기
- django extensions는 django를 더 쉽고 넓게 사용할 수 있도록 해주는 툴. 
- django extensions로 할 수 있는 기능들. https://pointer81.tistory.com/entry/%EC%9E%A5%EA%B3%A0%EC%9D%98-%EB%8B%A8%EC%A7%9D-%EC%B9%9C%EA%B5%AC-django-extensions

```terminal
python manage.py shell_plus --print-spl
```

- 그리고 해당 명령어로 django shell을 실행할 수 있는데, shell_plus는 django 기본에는 없다.     
  https://django-extensions.readthedocs.io/en/latest/installation_instructions.html 여기 **django extensions** 라는 라이브러리에서 shell_plus라는 명령이 지원된다. 그래서
```terminal
pip install django-extensions
```

- 해당 명령어를 통해 이 라이브러리를 설치해보자. 그리고 위의 공식문서를 보면, 그 다음에 해야 될 것들이 적혀있다.
  - 프로젝트 디렉터리 내부의 settings.py의 INSTALLED_APPS에 추가하기. 다만 app 이름은 django-extensions 이렇게 -이 포함되어 있지만, 이 자체가 package 이름이 되기 때문에 추가할 때는 'django_extensions', 이런식으로 추가하게 된다.

```python
INSTALLED_APPS = [
    # django apps
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # locals apps
    'blog1',
    'instagram',
    # third apps
    'django_extensions',
]
```

- settings.py의 INSTALLED_APPS 리스트를 다음과 같이 주석으로 구분하면 관리하기 편리해진다. 이제 다시 
```terminal
python manage.py shell_plus --help

usage: manage.py shell_plus [-h] [--bpython | --idle | --ipython | --lab | --kernel | --notebook | --plain | --ptipython | --ptpython]
                            [--connection-file CONNECTION_FILE] [--no-startup] [--use-pythonrc] [--print-sql] [--truncate-sql TRUNCATE_SQL]
                            [--print-sql-location] [--dont-load DONT_LOAD] [--quiet-load] [--vi] [--no-browser] [-c COMMAND] [--version]
                            [-v {0,1,2,3}] [--settings SETTINGS] [--pythonpath PYTHONPATH] [--traceback] [--no-color] [--force-color]
                            [--skip-checks]

Like the 'shell' command but autoloads the models of all installed Django apps.

optional arguments:
  -h, --help            show this help message and exit
  --bpython             Tells Django to use BPython.
  --idle                Tells Django to use Idle.
  --ipython             Tells Django to use IPython.
  --lab                 Tells Django to use JupyterLab Notebook.
  --kernel              Tells Django to use IPython Kernel.
  --notebook            Tells Django to use IPython Notebook.
  --plain               Tells Django to use plain Python.
  --ptipython           Tells Django to use PT-IPython.
  --ptpython            Tells Django to use PTPython.
  --connection-file CONNECTION_FILE
                        Specifies the connection file to use if using the --kernel option
  --no-startup          When using plain Python, ignore the PYTHONSTARTUP environment variable and ~/.pythonrc.py script.
  --use-pythonrc        When using plain Python, load the PYTHONSTARTUP environment variable and ~/.pythonrc.py script.
  --print-sql           Print SQL queries as they're executed
  --truncate-sql TRUNCATE_SQL
                        Truncate SQL queries to a number of characters.
  --print-sql-location  Show location in code where SQL query generated from
  --dont-load DONT_LOAD
                        Ignore autoloading of some apps/models. Can be used several times.
  --quiet-load          Do not display loaded models messages
  --vi                  Load Vi key bindings (for --ptpython and --ptipython)
  --no-browser          Don't open the notebook in a browser after startup.
  -c COMMAND, --command COMMAND
                        Instead of opening an interactive shell, run a command as Django and exit.
  --version             show program's version number and exit
  -v {0,1,2,3}, --verbosity {0,1,2,3}
                        Verbosity level; 0=minimal output, 1=normal output, 2=verbose output, 3=very verbose output
  --settings SETTINGS   The Python path to a settings module, e.g. "myproject.settings.main". If this isn't provided, the DJANGO_SETTINGS_MODULE
                        environment variable will be used.
  --pythonpath PYTHONPATH
                        A directory to add to the Python path, e.g. "/home/djangoprojects/myproject".
  --traceback           Raise on CommandError exceptions
  --no-color            Don't colorize the command output.
  --force-color         Force colorization of the command output.
  --skip-checks         Skip system checks.
```  

- shell_plus --help 명령어를 보면, 이렇게 다양한 shell 옵션이 나와있다. 예전에는 --notebook이라는 옵션이 default가 아니었다고 한다. 지금은 default라서 python manage.py shell_plus --print-sql 이렇게만 입력하면 notebook 서버가 구동이 된다.
- 만약, ipython 서버를 구동하고 싶다면

```terminal
python manage.py shell_plus --print-sql --ipython

# Shell Plus Model Imports
from instagram.models import Post
from django.contrib.admin.models import LogEntry
from django.contrib.auth.models import Group, Permission, User
from django.contrib.contenttypes.models import ContentType
from django.contrib.sessions.models import Session
# Shell Plus Django Imports
from django.core.cache import cache
from django.conf import settings
from django.contrib.auth import get_user_model
from django.db import transaction
from django.db.models import Avg, Case, Count, F, Max, Min, Prefetch, Q, Sum, When
from django.utils import timezone
from django.urls import reverse
from django.db.models import Exists, OuterRef, Subquery
Python 3.9.1 (v3.9.1:1e5d33e9b9, Dec  7 2020, 12:44:01) 
Type 'copyright', 'credits' or 'license' for more information
IPython 8.0.1 -- An enhanced Interactive Python. Type '?' for help.
```

- 이렇게 입력하면 ipython이 구동된다.
- **shell_plus의 특징은, 위의 코드처럼 필요한 몇몇 기능들을 자동으로 import 해준다. 기존의 python manage.py shell를 입력해서 장고와 연동된 shell에서는 직접 해당 모델을 import 해야했었는데, 이러한 작업을 미리 해주는 것이다.** 그래서, 모델을 Import 하지 않고

```terminal
In [1]: Post.objects.all()

Out[1]: SELECT "instagram_post"."id",
       "instagram_post"."message",
       "instagram_post"."photo",
       "instagram_post"."is_public",
       "instagram_post"."created_at",
       "instagram_post"."updated_at"
  FROM "instagram_post"
 LIMIT 21

Execution time: 0.000898s [Database: default]
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>
```

- 이렇게 바로 모델에 접근할 수 있다. 
- 그리고 우리가 처음에 python manage.py shell_plus --print-sql --ipython -> 이렇게 --print-sql이라는 옵션을 주었기 때문에, sql문을 같이 출력해주고 있다.

* * *

### 모델 클래스 내부에 Meta 클래스 설정해보기
- instagram 디렉터리 내부 models.py에서 설정
```python
class Post(models.Model):      
    message = models.TextField()    
    photo = models.ImageField(blank=True, upload_to='instagram/post/%Y/%m/%d')                             
    is_public = models.BooleanField(default=False, verbose_name='공개여부')    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True) 
    
    class Meta:
        ordering = ['-id']
```

- Post 모델안에 클래스 Meta 속성으로 id필드에 대한 역순을 설정해준다.
       
       
### Meta 클래스 설정 후, 다시 shell_plus로 django shell 실행
```terminal
python manage.py shell_plus --print-sql --ipython

In [1]: Post.objects.all()

Out[1]: SELECT "instagram_post"."id",
       "instagram_post"."message",
       "instagram_post"."photo",
       "instagram_post"."is_public",
       "instagram_post"."created_at",
       "instagram_post"."updated_at"
  FROM "instagram_post"
 ORDER BY "instagram_post"."id" DESC
 LIMIT 21

Execution time: 0.000849s [Database: default]
<QuerySet [<Post: 세번째 메세지>, <Post: 두번째 메세지>, <Post: 첫번째 메세지>]>
```

- Post 모델 내부에 Meta 클래스를 설정한 다음, 다시 shell_plus로 django shell를 실행하고 모델 데이터를 전부 조회해보면, 
  - 우리가 id필드의 역순으로 순서를 정의했기 때문에 -> 조회할 때도 ORDER BY "instagram_post"."id" DESC 이렇게 id필드의 역순으로 조회돼서 데이터를 볼 수 있게 된다.

### 위의 상태에서 추가로 다른 정렬을 적용
```terminal
In [2]: Post.objects.all().order_by('created_at')

Out[2]: SELECT "instagram_post"."id",
       "instagram_post"."message",
       "instagram_post"."photo",
       "instagram_post"."is_public",
       "instagram_post"."created_at",
       "instagram_post"."updated_at"
  FROM "instagram_post"
 ORDER BY "instagram_post"."created_at" ASC
 LIMIT 21

Execution time: 0.000226s [Database: default]
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>
```

- created_at 필드를 기준으로 정렬을 요청하면, 이렇게 기존의 Meta 클래스로 정의한 내용이 무시되고 우리가 지정한 정렬이 적용된다.

* * *
## QuerySet에 범위 조건 추가
- SELECT 쿼리에 OFFSET/LIMIT 추가, 슬라이싱을 통한 범위조건을 추가해주는 것이다.
- 문자열이나 리스트, 튜플에서의 슬라이싱과 거의 유사하나, 역순 슬라이싱은 지원하지 않는다. 즉, 음수 인덱스 접근이 안된다고 생각하자. 데이터베이스에서 지원하지 않기 때문.
  - **슬라이싱은 [start:stop:step]의 형태로 step은 몇 개씩 끊어서 가져올지와 방향을 정한다. 양수이면 오른쪽 / 음수이면 왼쪽 방향으로 이동하면서 가져온다.**
```terminal
>>> a = ['a', 'b', 'c', 'd', 'e']
# 2칸씩 이동하면서 가져옵니다.
>>> a[ : : 2 ]
['a', 'c', 'e']
```

```terminal
Post.objects.all()[:-2]
```

- 다음과 같은 음수 인덱스는 지원하지 않음. 만약 원래 의도한대로 마지막 2개를 출력하고 싶다면,

```terminal
In [2]: Post.objects.all().order_by('id')[:2]

Out[2]: SELECT "instagram_post"."id",
       "instagram_post"."message",
       "instagram_post"."photo",
       "instagram_post"."is_public",
       "instagram_post"."created_at",
       "instagram_post"."updated_at"
  FROM "instagram_post"
 ORDER BY "instagram_post"."id" ASC
 LIMIT 2

Execution time: 0.000346s [Database: default]
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>]>
```

- 이렇게 마지막 2개가 id가 가장 작다라고 표현해주면 된다.(Post 모델의 default를 내림차순으로 설정했으므로)

```terminal
In [4]: Post.objects.all()[1:3]

Out[4]: SELECT "instagram_post"."id",
       "instagram_post"."message",
       "instagram_post"."photo",
       "instagram_post"."is_public",
       "instagram_post"."created_at",
       "instagram_post"."updated_at"
  FROM "instagram_post"
 ORDER BY "instagram_post"."id" DESC
 LIMIT 2
OFFSET 1

Execution time: 0.000454s [Database: default]
<QuerySet [<Post: 두번째 메세지>, <Post: 첫번째 메세지>]>
```

- 슬라이싱을 적용했을 때 조회되는 내용이다. SQL문에서 끝에 LIMIT 2 OFFSET 1처럼 추가되어있는데, **LIMIT은 행을 얼마나 가져올지 정하는 것이고, OFFSET은 어디서부터 가져올지를 정하는 것이다.** 
  - 관련 내용은 https://zzang9ha.tistory.com/295 해당 블로그에 자세히 나와있다. 
  - 따라서 LIMIT 2 OFFSET 1은 데이터베이스의 행 2개를 가져오되, 2번째 행부터이니까 2,3번 행들을 가져오게 된다.
  - 위의 queryset의 결과는 queryset 타입으로 출력해준다. 반면에 step을 슬라이싱에 붙이게 되면,


```terminal
In [5]: Post.objects.all()[1:3:1]

SELECT "instagram_post"."id",
       "instagram_post"."message",
       "instagram_post"."photo",
       "instagram_post"."is_public",
       "instagram_post"."created_at",
       "instagram_post"."updated_at"
  FROM "instagram_post"
 ORDER BY "instagram_post"."id" DESC
 LIMIT 2
OFFSET 1

Execution time: 0.000438s [Database: default]
Out[5]: [<Post: 두번째 메세지>, <Post: 첫번째 메세지>]
```

- 반환값이 달라지게 된다. 그래서 **step이 들어가는 순간, 반환값이 리스트가 된다.**
- 그리고 step값이 sql에 반영되지 않는다. sql에는 step이라는 개념이 없기 때문이다. 이러한 step은 django의 queryset이 처리하게 된다.
  - 그래서 예를 들어 [1:3:2] 이러한 범위라면, 1:3을 가져온 다음에 -> 2 step을 뛰면서 새로운 리스트를 만들어주는 것이다.
  - step이 없을 때는, 반환값이 queryset으로 lazy하게 동작하는 객체이지만 / step이 있을 때는 리스트가 되기 때문에 lazy하지 않고 그 순간 데이터베이스에 쿼리를 던지게 된다.
