## UserForm 커스텀하기
- 회원가입 시 필요한 회원가입 폼인 UserForm를 커스텀할 수 있다. UserForm은 django.contrib.auth.forms 모듈의 UserCreationForm 클래스를 상속해서 만든다. 그래서 우리도 UserCreationForm 클래스를
오버라이딩해서 커스텀할 수 있다. 

- 장고에서 Form 인스턴스는 is_valid 함수를 가지고 있다. 그래서 유효성 검사를 알아서 해주기 때문에, 유효성 검사 시행 후 참인 값들만 cleaned_data라는 이름의 딕셔너리 형태로 담기게 된다. 그래서 
```python
...
# 이렇게 상속받아서 커스텀
class UserForm(UserCreationForm): 

# 또는 바로 오버라이딩도 가능
class UserCreationForm(forms.ModelForm):

def clean(self):
    nickname = self.cleaned_data.get('nickname')
...
```
- 이런식으로 사용할 수 있다.


## UserChangeForm
- 유저 정보를 바꾸기 위한 폼도 오버라이딩이 가능



### 관련 자료
- https://wikidocs.net/71303
- https://korinkorin.tistory.com/43
