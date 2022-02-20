## The view accounts.views.sign_up didn't return an HttpResponse object. It returned None instead.
- 위와 같은 이름의 에러가 났을 경우, 해당 View 함수에 return 값이 없다는 의미이다. 그래서 꼭 해당 View 함수 끝에 return 이렇게 코드를 입력했는지 확인하자.

```python
def sign_up(request):
    context = {}
    ...
    
    
    render(request, ~~~~)
```

- 이렇게 위와 같이 return을 적어주지 않으면 ValueError가 발생한다. 아래와 같이 꼭 View 함수 끝에는 return를 입력하자.

```python
def sign_up(request):
    context = {}
    ...
    
    
    return render(request, ~~~~)
```

