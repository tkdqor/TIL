## ListView 페이징 처리 UI 변경해보기
- django bootstrap4 활용해보기
  - 여기서 template tag라고 하는 것들은 django template tag에서 활용할 수 있는 function들의 모음이다. 즉, django bootstrap4는 template내에서 활용할 수 있는 여러 function들을 지원해준다.
  - 여러 기능 중 하나가 bootstrap_pagination이다.

- 공식문서는 https://django-bootstrap4.readthedocs.io/en/latest/installation.html 다음과 같고 설치를 먼저 해주면 된다.

```terminal
pip install django-bootstrap4
```

- 그리고 settings.py에 가서 설치 app 등록해주기
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
    'accounts',
    # third apps
    'bootstrap4',
    'django_extensions',
    'debug_toolbar',
]
```

- 이렇게 설치가 된 다음에 https://django-bootstrap4.readthedocs.io/en/latest/templatetags.html#bootstrap-pagination 해당 문서에서 bootstrap_pagination를 활용해보자.
  - 실제로 template에서 사용하려면 먼저 해당 template 위쪽에

```html
{% load bootstrap4 %}
...
 </table>

    {{ is_paginated }}
    {{ page_obj }}
    {% bootstrap_pagination page_obj %}
</body>
```

- 이렇게 page_obj를 넘겨주면 에러가 나지 않고 -> 실제 웹 페이지에서 볼 수 있는 페이징 UI가 나타난다. 위의 공식 문서에서 보면 다양한 설정들을 할 수 있다. size, url, extra, parameter_name
  - 이번에는 size를 바꿔보자. 

```html
...
{% if is_paginated %}    
  {% bootstrap_pagination page_obj size="large" %}
{% endif %}
```

- **django template language에서 template tag는 위에 bootstrap_pagination 여기에 해당하는 부분이다. 항상 중괄호와 %를 이용해서 사용해야 한다. template tag가 함수의 이름이라고 보면 된다.
  그리고, 원래 함수라면 bootstrap_pagination(page_obj, size="large") -> 이런식으로 호출할텐데 django template language에서는 소괄호와 콤마가 없다라고 보면 된다.**

- 또한, pagination의 위치를 가운데로 옮기고 싶다면 

<img width="709" alt="image" src="https://user-images.githubusercontent.com/95380638/154414347-29575279-5bf0-470e-8454-de803688716d.png">

- 해당 부분의 source를 우클릭으로 해서 새로운 창에 띄우고 봐보자.
  - 그러면, bootstrap_pagination(page, **kwargs): 이라는 함수가 내부적으로 get_pagination_context(**pagination_kwargs)를 호출하게 된다. 그리고 get_pagination_context(
    page, pages_to_show=11, url=None, size=None, justify_content=None, extra=None, parameter_name="page" 함수의 인자를 보면, justify_content라는 부분이 있다.
  - 그리고 더 밑으로 내려보면

```python
    if justify_content == "start":
        pagination_css_classes.append("justify-content-start")
    elif justify_content == "center":
        pagination_css_classes.append("justify-content-center")
    elif justify_content == "end":
        pagination_css_classes.append("justify-content-end")
```

- 다음과 같이, justify_content가 start, center, end인지에 따라서 -> 해당 pagination css 클래스 목록에다가 justify-content-start / justify-content-center / justify-content-end 라는 클래스를 추가해준다.

```html
...
{% if is_paginated %}    
  {% bootstrap_pagination page_obj size="large" justify_content="center" %}
{% endif %}
```

- 그래서 다음과 같이 justify_content 속성을 추가해주면, 정렬이 되는 것을 확인할 수 있다.
