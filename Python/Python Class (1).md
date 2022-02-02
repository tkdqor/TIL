## 자동차 함수 만들어보기
```python
ferrai = {
    "name": "Ferrai",
    "color": "Rosso Corsa",
    "fuel": 50,
    "mileage": 0
}

lamborghini = {
    "name": "Lamborghini",
    "color": "Giallo Spica",
    "fuel": 40,
    "mileage": 0
}

# 액셀 함수 정의
def ferrai_accel():
    ferrai['fuel'] -= 2
    ferrai['mileage'] += 10


def lamborghini_accel():
    lamborghini['fuel'] -= 3
    lamborghini['mileage'] += 12


# 액셀 밟기 전 상태 확인
print(ferrai)
print(lamborghini)

# 액셀 밟기
ferrai_accel()
lamborghini_accel()

# 액셀 밟은 후 상태 확인
print(ferrai)
print(lamborghini)

>>>
{'name': 'Ferrai', 'color': 'Rosso Corsa', 'fuel': 50, 'mileage': 0}
{'name': 'Lamborghini', 'color': 'Giallo Spica', 'fuel': 40, 'mileage': 0}
{'name': 'Ferrai', 'color': 'Rosso Corsa', 'fuel': 48, 'mileage': 10}
{'name': 'Lamborghini', 'color': 'Giallo Spica', 'fuel': 37, 'mileage': 12}
```

- 위와 같이 자동차를 딕셔너리로 정의하고 액셀에 대한 함수를 정의하는 코드가 있다고 생각해보자. 액셀을 밟으면 딕셔너리에 접근하여 연료가 줄어들고, 주행거리가 늘어나게 설정했다. 그리고 액셀을 밟기 전 상태를 확인하고 액셀 함수 호출 후, 다시 상태를 확인해보면, fuel과 mileage가 변한것을 알 수 있다.  
- 하지만, 새로운 자동차가 추가될 때 마다 또 딕셔너리를 정의해야하고 액셀 함수도 정의해야 한다. 딕셔너리나 함수를 보면 동일하게 작성되는 부분들이 많기 때문에 이러한 내용을 재활용하면 더 효율적으로 작성할 수 있을 것이다.


## 클래스(Class)
- 위와 같은 재활용 문제와 같이 필요성을 느끼고 만들어진 개념이 바로 클래스이다. **어떤 유사한 정보들이나 기능들로부터 반드시 지닐법한 것들만 추출해가지고 앞으로 계속해서 찍어낼 수 있게끔, 하나의 템플릿처럼 만든 것을 클래스라고 한다.** 클래스는 특정 개념을 추상화해서 템플릿으로 만들어서 분류하고 그렇게 만든 템플릿을 통해서 실제로 **object**를 찍어내고자 하는 용도로 사용된다.
  - ex) 자동차라면 이름 / 색상 / 연료 / 주행거리가 반드시 있을 것이고 액셀 / 브레이크 등 다양한 기능들이 공통적으로 있을 것이다. 그래서 자동차라면 가져야 할 데이터나 기능들을 정리해서 하나의 틀로써 정의한 것이다 클래스라고 보면 된다.

- 이러한 클래스를 사용하면, 유사한 포맷으로 구성되어 있는데 그 수치가 조금씩 다른 여러 데이터들을 쉽게 관리할 수 있고 단순히 정적인 데이터로 사용하는 게 아니라, 기능을 추가해서 사용할 수도 있다.
  - 과거 C언어와 같은 절차 지향 프로그래밍 시대를 지나, 클래스를 지원하는 C++, Java, Python 등의 프로그래밍 언어가 유행하게 되었다. 그래서 **객체 지향 프로그래밍**으로 프로그래밍 패러다임 쉬프트가 발생했다.


### 클래스 용어와 개념

```python
# Car라는 클래스 정의
class Car:
    def __init__(self, name, color, fuel):
        self.name = name
        self.color = color
        self.fuel = fuel
        self.mileage = 0

    def accel(self):
        self.fuel -= 2
        self.mileage += 10

    
car = Car("GENESIS", "black", 30)

print(car.name, car.color, car.fuel)
car.accel()
```
- 다음은 클래스를 활용해서 자동차를 구현해 본 코드이다. 이렇게 만든 클래스를 활용할 때에는, 클래스이름( ) 이렇게 해준 다음에 자동차를 표현하는데 필요한 필수 파라미터를 적어준다. 그리고 해당 내용을 car라는 소문자 변수에다가 저장해준다. 그렇게 정의하게 되면, car.name / car.accel()과 같은 형태로 Car 클래스의 정의된 내용들을 꺼내쓸 수 있다.

- **클래스는, 어떤 데이터나 기능을 다루기 위한 하나의 틀이다.** 유사한 데이터나 기능이 계속해서 반복적으로 사용되니까 이걸 아예 붕어빵틀처럼 하나의 템플릿으로 만들어준 다음, **붕어빵틀에서 붕어빵을 찍어내듯이 계속해서 object를 찍어내는 것이다.**
  - 또한, 틀이 같더라도 거기에 어떤 수치를 대입하느냐에 따라서 다른 물체가 생성된다. ex) 자동차 틀에 페라리, 빨간색을 넣어서 찍어내는 것과 람보르기니, 노란색을 넣어서 찍어내는 것은 다르게 나온다.

* * *

- 현실속에 표현하고자 하는 데이터와 기능을 컴퓨터 용어로 **object, 객체**라고 표현한다.
  - 그리고 해당 객체라면 반드시 지녀야 할 데이터나 기능들을 잘 추상화해서 만든 템플릿을 **클래스**라고 한다. (object -> class) 
  - 또한, 이 클래스를 바탕으로 프로그래밍 세상에 찍어낸 object를 우리는 **인스턴스**라고 부른다.(class -> Instance)

- 그래서 클래스를 통해 만들어진 object가 -> 컴퓨터 메모리 상에 실제로 할당되었을 때 우리는 인스턴스라고 부른다.


### 클래스 만드는 과정
1) 클래스를 만들 때 먼저 class라고 입력하고 한칸 띄운 다음, 우리가 만들고자 하는 원하는 클래스 이름을 첫 글자가 대문자로 시작하게끔 적어주고 콜론으로 마무리 한다.
2) 그 
