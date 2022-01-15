## Python Decorator @란?
- Decorator(python에서는 @로 표현)는 특정 함수를 Wrapping하고 Wrapping된 함수 앞뒤에 추가적으로 꾸며질 구문들을 정의해서 재사용 가능하게 해주는 것을 의미한다.
- @함수이름 -> 해당하는 함수의 메인 구문이 있고, 여기에 부가적인 구문을 추가하고 싶을 때 Decorator를 선언해서 사용하면 효율적으로 진행할 수 있다.

```python
import datetime

def datetime_decorator(func):
  def decorated():
      print(datetime.datetime.now())
      func()
      print(datetime.datetime.now())
  return decorated
```
이러한 함수가 있다고 가정할 때, 해당 함수를 Decorator해서 시간이 출력되는 앞, 뒤로 매번 다른 메시지를 출력할 수 있다.

```python
@datetime_decorator
def main_function_1():
    print ("MAIN function 1 start")
```
이렇게 Decorator를 해주고 **main_function_1()** 를 호출해주면 함수에 구문이 추가된 것을 확인할 수 있다.
-> 2021-01-16 17:24:54.972919  
   MAIN function 1 start  
   2021-01-16 17:24:54.973226
   

        
