## Python Decorator @란?
- Decorator(python에서는 @로 표현)는 특정 함수를 Wrapping하고 Wrapping된 함수 앞뒤에 추가적으로 꾸며질 구문들을 정의해서 재사용 가능하게 해주는 것을 의미한다.
- @함수이름 -> 해당하는 함수의 메인 구문이 있고, 여기에 부가적인 구문을 추가하고 싶을 때 Decorator를 선언해서 사용하면 효율적으로 진행할 수 있다.


### 함수 형태의 decorator
```python
import datetime

# @decorator 장식자를 이해해보자!


# 함수 형태로 decorator를 사용해보자!
# 먼저 반복되는 틀이 있는 함수가 정의되어있다고 생각해보자. 즉, datetime_decorator라는 함수가 decorator 역할을 하게 된다.
# 이 함수는 파라미터로 decorator가 적용될 함수를 받게끔 설정한다.
# decorator 역할을 하는 함수 내부에 함수를 선언하여 -> 반복되는 코드와 / decorator가 적용될 코드를 구분해준다.
# 적용될 코드에는 파라미터로 설정한 함수가 들어가게 한다.
# 마지막으로 return 값으로 내부에 선언한 함수를 정의한다.
def datetime_decorator(func):
    def decorated():
        print (datetime.datetime.now())
        func()
        print (datetime.datetime.now())
    return decorated    


# 이제 실제로 decorator를 사용해보자.
# 반복되지 않고 계속 변할 수 있는 함수 위에 @를 사용해서 위에 정의한 함수 이름을 입력하자.
# 반복되지 않는 새로운 함수는 -> decorator로 정의한 함수의 인자로 대입이 된다.
@datetime_decorator
def main_function_1():
    print("첫번째 함수가 실행!")

@datetime_decorator
def main_function_2():
    print("2번째 함수가 실행!")


# 이렇게 설정을 하고 함수를 호출해보면 반복되는 코드와 함께 decorator를 이용해서 다른 코드를 출력할 수 있다. 
main_function_1()
print()
main_function_2()

>>>
2022-02-04 00:22:48.518635
첫번째 함수가 실행!
2022-02-04 00:22:48.518676

2022-02-04 00:22:48.518682
2번째 함수가 실행!
2022-02-04 00:22:48.518687
```


### 클래스 형태의 decorator
```python
# 이제 class 형태로 decorator를 사용해보자!
# 먼저, 반복되는 틀을 가진 클래스를 정의하자. -> 그리고 그 클래스의 __init__메소드에는 파라미터를 받을 수 있게 설정하자.
# 그리고 __call__ 메소드로 반복되는 코드 형식을 정의하고 매번 달라지는 함수를 인자로 받아 정의한 self.func를 입력해주자. 

class DatetimeDecorator:
    def __init__(self, f):
        self.func = f

    def __call__(self, *args, **kwargs):
        print (datetime.datetime.now())
        self.func(*args, **kwargs)
        print (datetime.datetime.now())


# 이제 decorator를 사용해보자. 임의의 클래스를 정의한 다음에 반복되지 않고 변할 수 있는 함수 위에 우리가 정한 decorator 클래스를 @로 입력한다.
# 그리고 클래스 내부에 함수에서는 매번 다르게 들어갈 코드들을 입력하자.
class MainClass:
    @DatetimeDecorator
    def main_function1():
        print("첫번째 클래스 데코레이터 실행!")

    @DatetimeDecorator
    def main_function2():
        print("두번째 클래스 데코레이터 실행!")    


# 이렇게 정의하고 MainClass의 인스턴스를 만들고 함수를 실행보면 된다.
example = MainClass()
example.main_function1()
print()
example.main_function2()

>>>
2022-02-04 00:22:48.518714
첫번째 클래스 데코레이터 실행!
2022-02-04 00:22:48.518720

2022-02-04 00:22:48.518725
두번째 클래스 데코레이터 실행!
2022-02-04 00:22:48.518730
```


- 참고 블로그: https://bluese05.tistory.com/30
