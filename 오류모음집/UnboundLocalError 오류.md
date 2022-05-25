## UnboundLocalError 오류 발생
- 회원가입 시, 입력받아야 할 정보들을 입력하지 않고 회원가입 버튼을 누르면 UnboundLocalError at /accounts/sign_up/
local variable 'context' referenced before assignment 다음과 같은 오류 발생

- def sign_up이라는 함수 내부에 지역 변수 context를 정의하지 않은 채로, 함수 내부에서 회원가입 정보가 입력되지 않을 시 context에 error라는 key를 저장한 것이 원인으로 파악함
  - 에러 메세지를 해석해보면, 할당 전에 지역 변수 'context’가 참조되었다고 얘기해주고 있음

- **그래서 -> def sign_up 함수 바로 밑에 context = { 'cartItems': cartItems, } 이렇게 지역변수 context를 정의함으로써 오류 해결. 그러면 그 밑에 if문이 진행되면서 context 변수를 사용할 수 있게 된다.**


```python
# 회원가입 기능
def sign_up(request):
    cartItems_data = cartitems_count(request)  # products 앱 내부 cartitems_tag 모듈에 있는 cartitems_count 함수 가져오기
    cartItems = cartItems_data['cartItems']    # cartitems_count 함수의 cartItems 값 가져오기

    if request.method == 'POST':              # HTTP Request가 POST방식일 때, 
        if (request.POST.get('username') and  # POST로 전달한 username 데이터가 있고, 
            request.POST.get('password') and  # password 데이터가 있고,
            request.POST.get('password') == request.POST.get('password_check')):   # password와 password_check 데이터가 같다면

            new_user = User.objects.create_user(          # User 모델에 새로운 회원을 추가해주기 
                username = request.POST.get('username'),  # username과 password라는 이름으로 받은 데이터로 회원가입 진행
                password = request.POST.get('password'),
            )

            name = request.POST.get('nickname')           # 회원가입 시 입력한 nickname 데이터를 name 변수에 저장
            email = request.POST.get('email')             # 회원가입 시 입력한 email 데이터를 email 변수에 저장
            problem = request.POST.get('social')          # 회원가입 시 선택한 problem 데이터를 problem 변수에 저장
            customer = Customer(user=new_user, name=name, email=email, problem=problem)   
            # 회원가입이 되면, User 모델과 1:1관계로 되어있는 Customer 모델의 user 필드 / name 필드 / email 필드 / problem 필드에 값을 저장
            customer.save()


            auth.login(request, new_user)             # 회원가입이 완료 되면, auth 모듈의 login 함수를 이용해서 해당 유저를 로그인 시키기

            return redirect('products:index')            # 로그인이 된 다음에는 메인 페이지 보여주기

        else:
            context['error'] = '아이디와 비밀번호를 다시 입력해주세요.' 
            # 만약, 조건을 만족하지 못할 때는 context 딕셔너리에 error라는 key를 저장해서 오류 메세지를 출력하게끔 하기    
            
    context = { 'cartItems': cartItems, }

    return render(request, 'accounts/sign_up.html', context)   # POST방식이 아닌 GET방식의 HTTP Request라면 회원가입 페이지 보여주기
```


- 위의 코드처럼 context = { 'cartItems': cartItems, } 이렇게 context를 정의하는 코드가 함수 바로 밑에 정의되어 있지 않고 마지막에 되어있다. 
  - 이 상태에서 context['error'] = '아이디와 비밀번호를 다시 입력해주세요.' 다음과 같은 코드를 실행시키면 -> 함수 내부에 지역 변수 context가 정의되지 않았는데 사용하게 되는 것이다.

```python
def sign_up(request):
    cartItems_data = cartitems_count(request)  # products 앱 내부 cartitems_tag 모듈에 있는 cartitems_count 함수 가져오기
    cartItems = cartItems_data['cartItems']    # cartitems_count 함수의 cartItems 값 가져오기
    context = { 'cartItems': cartItems, }

    if request.method == 'POST':              # HTTP Request가 POST방식일 때, 
        if (request.POST.get('username') and  # POST로 전달한 username 데이터가 있고, 
            request.POST.get('password') and  # password 데이터가 있고,
            request.POST.get('password') == request.POST.get('password_check')):   # password와 password_check 데이터가 같다면

            new_user = User.objects.create_user(          # User 모델에 새로운 회원을 추가해주기 
                username = request.POST.get('username'),  # username과 password라는 이름으로 받은 데이터로 회원가입 진행
                password = request.POST.get('password'),
            )

            name = request.POST.get('nickname')           # 회원가입 시 입력한 nickname 데이터를 name 변수에 저장
            email = request.POST.get('email')             # 회원가입 시 입력한 email 데이터를 email 변수에 저장
            problem = request.POST.get('social')          # 회원가입 시 선택한 problem 데이터를 problem 변수에 저장
            customer = Customer(user=new_user, name=name, email=email, problem=problem)   
            # 회원가입이 되면, User 모델과 1:1관계로 되어있는 Customer 모델의 user 필드 / name 필드 / email 필드 / problem 필드에 값을 저장
            customer.save()


            auth.login(request, new_user)             # 회원가입이 완료 되면, auth 모듈의 login 함수를 이용해서 해당 유저를 로그인 시키기

            return redirect('products:index')            # 로그인이 된 다음에는 메인 페이지 보여주기

        else:
            context['error'] = '아이디와 비밀번호를 다시 입력해주세요.' 
            # 만약, 조건을 만족하지 못할 때는 context 딕셔너리에 error라는 key를 저장해서 오류 메세지를 출력하게끔 하기     


    return render(request, 'accounts/sign_up.html', context)   # POST방식이 아닌 GET방식의 HTTP Request라면 회원가입 페이지 보여주기
```

- **그래서 다음과 같이 def sign_up(request): 바로 밑에 context = { 'cartItems': cartItems, } 를 입력해주면 오류가 해결된다.**

