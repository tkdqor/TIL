## 식당 예약 사이트 설계 및 모델링 예시
- 식당을 예약하고 후기를 남길 수 있는 사이트를 만들어보자.
  - 로그인을 하지 않았을 경우에는 첫 페이지로 추천 식당과 최근 등록된 식당, 인기있는 식당을 보여주기. 식당 1개를 클릭하면 식당의 주소가 이미지들을 표시. 하단에는 후기들을 목록으로 표시. 로그인과 회원가입도 가능하게 구성. 로그인은 소셜 로그인으로 구글 등을 지원해주기.
  - 로그인을 했다면, 식당 상세화면에서도 예약이 가능하고 예약화면에서 원하는 시간을 선택하면 결제창이 열리고 결제가 진행되게끔 한다. 결제까지 마무리가 되면 내 예약화면에서 본인의 예약현황을 볼 수 있게 하기. 로그인 시 접근가능한 메뉴인 마이페이지에서는, 예약 내역과 후기 목록을 볼 수가 있고 여기서 예약 취소, 후기 생성 및 수정과 삭제도 가능하게끔 하기. + 추가로 관리자 권한을 가진 계정으로 로그인을 한다면 전체 등록된 식당의 목록을 볼 수 있고 식당을 새로 추가하거나 삭제, 수정을 할 수 있다.

- **모델링 해보기**
  - 일단 django가 기본적으로 제공하는 User 모델의 계정이 있다. django의 기본 User 모델을 사용해서 형제 계정을 만들것이다. 그리고 django의 all-auth라는 것을 활용해서 SNS 계정을 구현할 예정이다. 
  - **유저 관련 모델** : 그리고 사용자의 프로필 모델을 구성해서 닉네임이나 사진 등을 받는다. 그리고 내가 이메일로 가입을 했다면 이메일에 대한 인증 정보들을 담은 모델도 구현해야 한다.
  - **식당 관련 모델** : 카테고리(이름, 생성일) / 식당(소속 카테고리, 이름, 대표이미지, 주소, 전화번호, 위도, 경도, 등록일, 수정일, 메뉴 정보, 설명) / 식당이미지(식당 FK, 이미지) / 추천식당(식당 FK, 정렬순서, 등록일) 이렇게 4개의 모델로 구현이 될 예정이다. 나중에는 식당 운영자를 위해서 해당 권한을 명시하는 추가적인 모델을 구현할 예정이다.(식당 FK, 유저 FK, 등록일)
    - 식당 정보의 경우, 식당 하나에 대해서는 여러 개의 이미지가 담길 수 있을 것이다. 식당의 대표 이미지 말고도 여러 가지 슬라이드로 넘기면서 볼 수 있는 여러 이미지가 표시된다. 그래서, 이미지 같은 경우 다른 모델로 분리가 되어야 한다.
    - 그리고 우리가 보통 어떤 사이트를 들어가면 광고라던가 메인에 등장하는 추천 식당이나 상품들이 뜨게 된다. 그런 건 식당 모델과 별개로 내가 이 식당을 어느 화면에 노출시킬지, 어떤 순서로 배열을 할지 정하는 별도의 모델이 존재하게 된다. 그래서 실제로 우리가 메인 화면에서 조회를 할 때는 -> 해당 모델을 조회한 뒤에 조인을 써서 결합된 식당 리스트를 가지고 오는 것이다.
  - **예약 관련 모델** : 식당 예약 시간 슬롯(요일 별)(식당 FK, 요일, 시간, 예약 가능 수, 예약금액, 등록일) / 예약(유저 FK, 식당 FK, 예약 시간 슬롯 FK, 주문번호, PG사 일련번호, 결제수단, 결제 상태, 예약금액, 생성시각, 결제시각, 취소시각, 예약자명, 예약자 전화번호, 예약자 코멘트) / 결제내역(예약 FK, 처리 금액, 처리 시각) / 후기(유저 FK, 식당 FK, 예약 FK, 코멘트, 평점, 등록일) 이렇게 4개의 모델로 나눠지게 된다.
    - 식당의 경우에는, 그 식당에서 어떤 요일에 예약이 가능한지 시간대가 담길 수 있어야 할 것이다. 요일별로 각각의 레코드를 만들어서 그 레코드마다, 예를 들어 9시 한 칸, 10시 한 칸 이런식으로 요일별로 예약가능한 시간대와 가능한 예약 손님 수를 구성해서 모델을 구성하자.
    - 또한, 사용자의 식당 예약 정보를 저장하기 위한 예약 모델과 예약의 결제와 환불 처리 건마다 이력이 달리지니까 그것에 대한 모델을 따로 구성해야 한다. 예를 들어 예약을 했다고 하더라도 우리가 어떤 자리를 맡아서 예약을 하면 그 사건은 1개이다. 그것을 대표하는 모델을 따로 만들어야 한다. 그것과 별개로 결제를 생각해보면 -> 결제는 예약과 별개로 이루어진다. 선결제가 이루어지는 경우가 있을수도 있다. 물론 우리의 프로젝트에서는 결제가 이루어져야지 예약이 가능하도록 구성을 할 예정이지만 실제를 생각해보면 처음에는 5명을 예약했다가 1명을 환불받을 때도 있을 것이다. 2명 환불했다가 3명을 추가할 수도 있다. 그래서 결제와 예약은 별도의 모델로 구성해야 한다. 그래서 환불 처리건마다 이력도 결제할 수 있는 모델을 구성한다. 
    - 여기에 예약 후기를 남길 수 있도록 하는 후기 모델도 저장한다. 이 모델은 예약 모델과도 연관되어 있고 식당 모델과도 연관되어 있다. 

- **이메일 인증 과정**
  - 유저 기본 계정 정보는 기본 User 모델을 그대로 사용하기. 프로필 정보는 별도로 만들어서 관리자 여부도 같이 표시할 예정.
  - 이메일로 가입했을 경우에는 이메일 인증도 하게 할 것이다. 이메일 인증 시도마다 이메일로 발송될 고유한 링크를 생성하고 이를 기억하기 위해서 이메일 인증 정보를 위한 모델도 만들어둔다. 그리고나서 사용자 프로필에서 계정의 인증 여부를 False로 해두었다가 가입한 이메일로 고유한 링크를 전송해서 사용자가 이메일로 로그인한 후에 그 링크를 눌러야지 인증 여부를 True로 바꾸고 인증정보의 완료 시각을 저장한다. 
    - 가입완료 -> 이메일 + 검증 문자열 -> 이메일 인증(링크) -> 검증 문자열 -> 검증 페이지
    - 이렇게 가입이 완료된 시점에 바로 검증이 끝나는 것이 아니라, 가입이 완료가 되면 그 시점에 메일을 하나 보내서 거기에 검증문자를 합친 어떠한 링크를 생성한다. 그래서 그 링크가 담긴 이메일이 발송이 되어 사용자가 그걸 받아보고, 거기에 담긴 링크를 클릭하면 -> 우리 django의 특정 페이지로 이동해서 우리가 query parameter로 고유한 문자를 받아서 그 검증한 값이 사용자에 저장된 검증 키랑 일치하면 -> 그 때서야 인증과정이 끝나는 것이다. 
  - 그리고 **SNS로 로그인을 할 경우,** SNS 계정에서 보통 이메일 정보를 가져올 수 있다. 이 이메일을 이메일 회원가입처럼 그대로 User 모델의 계정 이름으로 쓰되, 인증은 완료된것으로 간주하고 SNS 계정에 대한 고유 정보를 저장할 SNS 계정 모델을 구성해야 한다. 카카오로 로그인을 하든, 네이버나 구글로 로그인을 하든, 이메일 정보가 동일하다면 하나의 유저에 여러 SNS 정보가 연결된 형태로 저장될 수 있고 또는 하나씩 구성이 되더라도 네이버나 구글, 카카오의 경우에는 이메일 가입과는 다르게 부가적인 정보를 필요로 하기 때문에, 그런식으로 하기 위해서 SNS계정과 관련된 모델을 따로 구성하게 된다.


### 실습해보기
- table_bookings라는 이름의 프로젝트를 생성해보자. 프로젝트 이름에 그냥 - 은 들어갈 수 없다. 그리고 web이라는 이름의 App를 만들어준다.
- 그리고나서 settings.py로 들어가 설치한 App이름을 적어주고, 
```python
STATICFILES_DIRS = [
    BASE_DIR / 'static',
]
```

- 이렇게 static 파일 디렉터리도 설정해준다. 그리고 이어서 web 디렉터리 안에 static, templates 라는 디렉터리를 만들어준다. templates 안에는 common.html를 만들어서
```html
{% load static %}
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}식당예약 사이트{% endblock %}</title>

    <link rel="stylesheet" href="{% static 'styles.css' %}">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.0/dist/css/bootstrap.min.css" integrity="sha384-B0vP5xmATw1+K9KRQjQERJvTumQW0nPEzvF6L/Z6nronJ3oUOFUFpCjEUQouq2+l" crossorigin="anonymous"><link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.0/dist/css/bootstrap.min.css" integrity="sha384-B0vP5xmATw1+K9KRQjQERJvTumQW0nPEzvF6L/Z6nronJ3oUOFUFpCjEUQouq2+l" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@4.6.0/dist/js/bootstrap.bundle.min.js" integrity="sha384-Piv4xVNRyMGpqkS2by6br4gNJ7DXjqk09RmUpJ8jgGtD7zP9yug3goQfGII0yAns" crossorigin="anonymous"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.5.0/font/bootstrap-icons.css">

    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
    <script src="https://cdn.jsdelivr.net/npm/flatpickr"></script>
</head>
<body>
<div class="container">
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <div class="container-fluid">
            <a href="/"><span class="navbar-brand mb-0 h1">TABLE BOOKING</span></a>

            <ul class="nav justify-content-end">
                {% if user.is_authenticated %}
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'booking-history' %}">예약 내역</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'review-history' %}">후기 내역</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'profile' %}">정보 수정</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'logout' %}">로그아웃</a>
                </li>
                {% else %}
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'register' %}">회원가입</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'login' %}">로그인</a>
                </li>
                {% endif %}
            </ul>

        </div>
    </nav>

    {% if messages %}
    <div class="mt-2">
        {% for message in messages %}
        <div class="alert alert-{{ message.tags }}" role="alert">
            {{ message }}
        </div>
        {% endfor %}
    </div>
    {% endif %}

    <div class="mt-4 col-12 mb-4">
        {% block content %}
        {% endblock %}
    </div>
</div>
</body>
</html>
```

- 위와 같은 코드를 입력해준다.

- 또한, github를 하는 것을 대비해서 프로젝트 디렉터리 상위 디렉터리 안에다가 .gitignore라는 파일을 생성해주자. -> 나는 mytasklist에서 진행.
```
# Created by https://www.toptal.com/developers/gitignore/api/django
# Edit at https://www.toptal.com/developers/gitignore?templates=django

### Django ###
*.log
*.pot
*.pyc
__pycache__/
local_settings.py
db.sqlite3
db.sqlite3-journal
media
```

- 해당 파일은 내가 git init을 선언할 그 장소에다가 만들어둬야 한다. 또한, 나중에 배포할 때나 협업하기 위해서 
```terminal
pip freeze > requirements.txt
```

- 이렇게 명령어를 입력해서 requirements.txt 파일을 생성해주면 된다. 그 뒤에 migrations와 migrate까지 진행해주기.


