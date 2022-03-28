## Django의 기본 로직: URLs and Views (2)


### 계산기 기능 이어서 만들기
- 앞서 Views.py에서 작성한 코드 중, render를 command 누르고 입력해보면 
```python
def render(
    request, template_name, context=None, content_type=None, status=None, using=None
):
    """
    Return an HttpResponse whose content is filled with the result of calling
    django.template.loader.render_to_string() with the passed arguments.
    """
    content = loader.render_to_string(template_name, context, request, using=using)
    return HttpResponse(content, content_type, status)
```

- **이렇게 render안에도 결국에는 HttpResponse를 응답하게 되어있다. html을 편하게 응답하기 위해서 django가 render를 만들어놓은 것이다.** 그래서 우리는 html로 응답할 때 render를 사용한다.

* * *
- 또한, 계산기라고 하면 사용자가 숫자와 사칙연산을 입력해야 할 것이다.
  - 먼저 View 코드를 작성해보자.

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def calculator(request):

    # 1. 데이터 확인
    num1 = request.GET.get('num1')
    num2 = request.GET.get('num2')
    operators = request.GET.get('operators')


    # 2. 계산
    if operators == '+':
        result = num1 + num2
    elif operators == '-':
        result = num1 - num2
    elif operators == '*':   
        result = num1 * num2 
    elif operators == '/':   
        result = num1 / num2     
    else:
        result = 0    


    # 3. 응답

    return render(request, 'calculator.html')
```


- 일단 우리가 3가지 값이 필요하다. 숫자 2개와 연산자를 받아야 한다.
- request.GET 메소드를 사용해서 GET방식으로 보낸 데이터를 받아주자. 사용자가 브라우저를 통해서 server 값을 보낼 때 우리가 값을 받을 수 있는 방법이다.
- 이렇게 3가지 값을 받은 다음에 우리가 연산을 해줘야 한다.
  - 연산자에 따라서 계산을 해주는거니까 if문을 사용해서 계산을 해주자.
  
  
- 브라우저에서 한 페이지를 새로고침했을 때,
```terminal
[28/Mar/2022 02:44:48] "GET /calculator/ HTTP/1.1" 200 349
```
- **이렇게 뜨는 것이 바로 요청 로그이다.**

* * *
- 그리고 html에서 사용자가 숫자와 연산자를 선택할 수 있게끔 해주고 server에 보내줘야 한다. 그래서 calculator.html에 가서 form tag를 사용하자.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>calculator</title>
</head>
<body>
    <h1>김상백이 만든 계산기 입니다.</h1>
    계산기 개발 중입니다.

    <form action="">
        <input type="text" name="num1">
        <input type="text" name="num2">
        <input type="text" name="operators">
        <input type="submit">
    </form>
</body>
</html>
```

- 그리고 form 내부에 input tag를 3개 만들고 name으로 받고자하는 데이터의 이름을 정해준다. 마지막으로 input를 1개 더 만들어서 type를 submit으로 수정해준다. 즉, 요청을 보내는 버튼이다.
- 이렇게 코드를 입력하고 브라우저를 새로고침하면 --> 입력받을 수 있는 창이 3개가 뜨고 제출버튼을 확인할 수 있다.
  - 그래서 실제로 숫자와 연산자를 입력해보면 아직 브라우저에 출력되지는 않지만, 터미널에

```terminal
[28/Mar/2022 02:55:15] "GET /calculator/?num1=1&num2=3&operators=%2B HTTP/1.1" 200 538
```

- 이렇게 다른 로그가 남겨진다.

* * *
- **html에서 데이터를 출력하기 위해서는 View에서 데이터를 넘겨줘야 한다.**
- 그래서 우리가 Views.py에서 설정한 result라는 변수값을 template으로 넘겨주자.

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def calculator(request):

    # 1. 데이터 확인
    num1 = request.GET.get('num1')
    num2 = request.GET.get('num2')
    operators = request.GET.get('operators')

    # 2. 계산
    if operators == '+':
        result = num1 + num2
    elif operators == '-':
        result = num1 - num2
    elif operators == '*':   
        result = num1 * num2 
    elif operators == '/':   
        result = num1 / num2     
    else:
        result = 0    


    # 3. 응답

    return render(request, 'calculator.html', {'result': result})
```

- 넘겨줄 때는 render 함수의 3번째 인자로 입력한다. 여기서 딕셔너리를 활용해 result라는 변수를 key와 value로 정해준다.
  - 그러면 html에서 result라는 값을 result라는 변수 이름으로 사용할 수 있는 것이다.


- 다시 calculator.html로 돌아와서 django template language를 사용하자.
```html
...
    <form action="">
        <input type="text" name="num1">
        <input type="text" name="num2">
        <input type="text" name="operators">
        <input type="submit">
        <br/>
        결과값 : {{ result }}
    </form>
```

- 이렇게 result 변수값을 출력해줄 수 있다. 
- 그리고나서 브라우저에서 1,3,+를 입력하면 결과값으로 13이 나오게 된다.
  - 다시 views.py를 보면, 

```python
num1 = request.GET.get('num1')
num2 = request.GET.get('num2')
...
```

- **이렇게 우리가 받아온 값들이 숫자가 아닌 텍스트로 받은 것이다. 지금은 '1' + '3' 이 되어 13이 출력되었으나, 우리가 원하는 건 1 + 3 = 4 이렇게 되는 것을 원하는 것이다.**
  - 그래서 num1, num2를 integer로 타입변환을 해주자.

```python
...
    # 2. 계산
    if operators == '+':
        result = int(num1) + int(num2)
    elif operators == '-':
        result = int(num1) - int(num2)
    elif operators == '*':   
        result = int(num1) * int(num2) 
    elif operators == '/':   
        result = int(num1) / int(num2)     
    else:
        result = 0    
        ...
```

- 원래는 사용자가 입력한 값이 텍스트로 넘어오게 된다. **클라이언트가 server로 데이터를 보낼 때 --> 무조건 텍스트(문자형)를 기준으로 들어오게 된다.** 따라서, 숫자로 계산하고 싶다면 타입을 변환시켜주면 된다.
- 이제는 브라우저를 새로고침해서 값을 입력해보면 정상적으로 계산기 기능이 작동하는 것을 확인할 수 있다.

* * * 
- 프로그래밍을 할 때, **사용성**을 항상 고려해야 된다. 즉, 편하게 사용할 수 있게 만들어야 한다.
- 다시 html로 가서 수정하자.

```html
<form action="">
        <input type="text" name="num1">
        <select name="operators">
            <option value="+">+</option>
            <option value="-">-</option>
            <option value="*">*</option>
            <option value="/">/</option>
        </select>
        <input type="text" name="num2">
        <input type="submit">
        <br/>
        결과값 : {{ result }}
</form>
```

- 우리가 1+1 이렇게 하듯이 input의 순서를 바꿔주었다. 그리고 가운데 연산자 부분은 선택을 할 수 있게 바꿔줘야 한다. 
  - 그래서 select라는 tag를 사용해볼 수 있다. select tag 자체에 name를 operators로 설정하고, 선택을 해줘야하기 때문에 select안에 option tag가 들어가야 한다. 그리고 어떤 걸 선택했을 때 어떤 값을 넘겨줄지 value element에 설정할 수 있다.

<img width="354" alt="image" src="https://user-images.githubusercontent.com/95380638/160320937-e067c9b1-01f8-4015-bcef-a74efb819a3f.png">

- 그러면 이렇게 선택할 수 있게 해줄 수 있다.

- **프로그래밍을 구성할 때, 주석을 사용해서 먼저 순서나 흐름을 만들어놓고 난 뒤에 코드를 입력하는 것이 훨씬 도움이 된다.**
