## 클래스의 상속
- 이미 만들어져 있는 클래스를 바탕으로 상속을 받아 우리가 원하는 대로 재사용해서 커스터마이징을 할 수 있다. 
- 상속 시 부모 클래스에 있던 모든 변수와 메소드를 문자 그대로 상속받아서 전부 사용할 수 있다. 원한다면 그대로 사용하는 것이 아니라 새롭게 재정의를 해서 사용할 수도 있다.
- super 함수를 통해서 기존 부모 클래스 내부에 있었던 데이터나 기능을 접근해서 사용할 수 있다.
  - super 함수를 사용해서 부모 클래스인 Car 클래스에 접근해서 내부에 있는 던더init 메소드를 가져와서 그걸 대신 호출하고 값을 넣어줄 수 있다. 또는 부모 클래스 내부에 있는 메소드를 가져와서 수정할 수도 있다.


### 상속된 클래스 만들기

```python
class Car:
    count = 0

    def __init__(self, name, color, fuel):
        self.name = name
        self.color = color
        self.fuel = fuel
        self.mileage = 0
        Car.count += 1


    def accel(self):
        self.fuel -= 2
        self.mileage += 10


    def __str__(self):
        return f'{self.name} {self.color} {self.fuel} {self.mileage}'    


# 위에있는 부모 클래스 상속받기
class Ferrari(Car):
    def __init__(self):
        self.name = "Ferrari"
        self.color = "Rosso Corsa"
        self.fuel = 50
        self.mileage = 0


ferrari = Ferrari()
print(ferrari)
```

- 맨 처음 클래스의 이름을 적고 괄호 열고 닫은 다음에 상속받고자 하는 클래스의 이름을 적어주고 콜론을 입력하면 된다. 그 다음에는 자유롭게 덮어쓸 것은 덮어쓰고 고칠 건 고치면 된다.
  - 상속해서 만든 클래스로 인스턴스를 생성하고 출력해보면 부모 클래스의 속성을 이어받아 똑같이 적용되는 것을 확인할 수 있다.
- 그런데, 위의 코드는 Ferrari 클래스를 생성할 때 기존에도 있는 던더init 메소드를 다시 정의하고 있다. 이미 부모 클래스에 정의되어있기 때문에 그걸 호출하는 방식으로 해보자.

```python
class Car:
    count = 0

    def __init__(self, name, color, fuel):
        self.name = name
        self.color = color
        self.fuel = fuel
        self.mileage = 0
        Car.count += 1


    def accel(self):
        self.fuel -= 2
        self.mileage += 10


    def __str__(self):
        return f'{self.name} {self.color} {self.fuel} {self.mileage}'    


# 위에있는 부모 클래스 상속받기
class Ferrari(Car):
    def __init__(self):
        super().__init__("Ferrari", "Rosso Corsa", 50)
        self.slogan = "We are the competition."

    def __str__(self):
        return f'{self.name} {self.color} {self.fuel} {self.mileage} {self.slogan}'  


ferrari = Ferrari()
print(ferrari)

>>>
Ferrari Rosso Corsa 50 0 We are the competition.
```

- super() 함수는 부모 클래스에 접근할 수 있게 해준다. 따라서 super().던더init던더() 해당 코드가 실행될 때 -> 부모 클래스에 있는 def 던더init던더(self, name, color, fuel): 이 실행된다고 볼 수 있다.
- 그래서 super().던더init던더("Ferrari", "Rosso Corsa", 50) 이렇게 입력해주면 -> 부모 클래스의 던더init 메소드가 실행되고 인자를 넣어주는 것이다. 
- 또한, 부모 클래스 대비 차이나는 부분에 대해서 추가할 수 있다.  self.slogan = "We are the competition." 이렇게 추가해주면 된다.
