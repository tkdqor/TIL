### 회원가입 시 User - Profile 모델로 정보받기
- template 코드
```html
{% extends 'base.html' %}
{% load static %}

{% block style %}
{% endblock %}

{% block content %}

<!-- 에러 메세지 띄우기 -->
<div>
    {% if error %}
    <div class="alert alert-danger mt-3">
        {{ error }}
    </div>
</div>
    {% endif %}



<div>
    <h1>회원가입</h1>
</div>

<!-- 회원가입 정보 받기 -->
<div class="row">
    <form method="POST" action="{% url 'accounts:sign_up' %}" enctype="multipart/form-data">  
        {% csrf_token %}
        
        <div class="mb-2 col-lg-3">
            <label class="form-label" for="username">아이디</label>
            <input class="form-control" type="text" id="username" name="username" placeholder="아이디">
        </div>

        <div class="mb-2 col-lg-3">
            <label class="form-label" for="password">비밀번호</label>
            <input class="form-control" type="password" id="password" name="password" placeholder="비밀번호">
        </div>

        <div class="mb-2 col-lg-3">
            <label class="form-label" for="password_check">비밀번호 확인</label>
            <input class="form-control" type="password" id="password_check" name="password_check" placeholder="비밀번호 확인">
        </div>

        <div class="mb-2 col-lg-3">
            <label class="form-label" for="email">이메일</label>
            <input class="form-control" type="email" id="email" name="email" placeholder="이메일">
        </div>

        <div class="mb-2 col-lg-3">
            <label class="form-label" for="age">나이</label>
            <input class="form-control" type="number" id="age" name="age" placeholder="나이">
        </div>

        <div class="mb-2 col-lg-3">
            <p class="mt-3">성별</p>
            <input type="checkbox" id="gender" name="gender" value="남자">
            <label class="form-label" for="gender">남</label>
            <input type="checkbox" id="gender" name="gender" value="여자">
            <label class="form-label" for="gender">여</label>
        </div>

        <div class="mb-2 col-lg-3">
            <label class="form-label" for="image">프로필 사진</label>
            <input class="form-control" type="file" id="image" name="image" placeholder="프로필 사진 업로드">
        </div>

        <div class="mb-2">
            <button type="submit">회원가입</button>
        </div>

    </form>
</div>


{% endblock %}
```


- Views.py 코드
```python
# 회원가입 기능
def sign_up(request):
    context = {}

    if request.method == 'POST':
        if (request.POST.get('username') and  # 회원가입 시 받은 아이디와 비밀번호 그리고 이메일이 모두 있는지 확인
            request.POST.get('password') and  
            request.POST.get('email') and
            request.POST.get('password') == request.POST.get('password_check')):
            # 그리고 받은 비밀번호와 비밀번호 확인 내용이 일치하는지 확인

            new_user = User.objects.create_user(    # User 모델에 새로운 유저 생성 / 입력된 아이디와 비밀번호로 생성
                username = request.POST.get('username'),
                password = request.POST.get('password'),
            )

            auth.login(request, new_user)   # 위에서 생성한 유저를 로그인시키기

            user = request.user                  # 로그인된 유저를 user 변수에 저장 / 밑에 코드들도 회원가입 시 받은 데이터를 각 변수에 저장
            email = request.POST.get('email')
            age = request.POST.get('age')
            gender = request.POST.get('gender')
            image = request.FILES.get('image')
            profile = Profile(user=user, email=email, mbti='none', type='none', age=age, gender=gender, introduce='none', image=image)
            profile.save()                       # 각 변수를 Profile 모델 필드에 저장하고 save()

            return redirect('index')

        else:
            context['error'] = '아이디, 비밀번호 그리고 이메일을 다시 입력해주세요!'    


    return render(request, 'accounts/sign_up.html', context)
```

- 마이페이지 코드
```html
{% extends 'base.html' %}
{% load static %}

{% block style %}
<link rel="stylesheet" href="{% static 'accounts/mypage_style.css' %}">
{% endblock %}

{% block content %}

<div>
    <h1>마이페이지</h1>
</div>

<p>아이디 : {{ profile.user }}</p>
<p>이메일 : {{ profile.email }}</p>
<p>나이 : {{ profile.age }}</p>
<p>성별 : {{ profile.gender }}</p>
<p>프로필 사진 : <img src="{{ profile.image.url }}" alt="프로필 사진"></p>

<hr>
```
