## Python class 예시
```python
class Car:
    def __init__(self):
        print("자동차를 만듭니다.")


# 이렇게 클래스 자체를 함수처럼 호출해서 사용할 수도 있다.
Car()

>>>
자동차를 만듭니다.
```

- 이렇게 Car라는 클래스를 함수처럼 호출해서 인스턴스를 만들지 않아도 사용할 수 있다. 이 때는, 내부적으로 던더init 메소드가 호출된 것이다.

```python
class Car:
    def __init__(self, name, color, fuel):
        print("자동차를 만듭니다.")
        self.name = name
        self.color = color
        self.fuel = fuel
        self.mileage = 0

genesis = Car("GENESIS", "black", 50)
ferrari = Car("Ferrari", "Rosso Corsa", 40)

print(genesis)
print(ferrari)

>>>
자동차를 만듭니다.
자동차를 만듭니다.
<__main__.Car object at 0x100f27fd0>
<__main__.Car object at 0x100f27f10>
```  


- 개발자들은 변수의 네이밍, 함수의 네이밍에 굉장히 민감하다. 따라서 변수의 이름만 봐도 그 변수 내부에 어떠한 값이 저장되어 있는지 유추할 수 있게끔 설정하는 것이 좋다. 그래서 위의 코드처럼 새롭게 만든 Car 인스턴스 변수의 이름을 genesis / ferrari 이런식으로 설정해보자.
- print 함수를 사용해서 Car 인스턴스를 호출해보면, 먼저 등호 오른쪽에 클래스가 호출되어 "자동차를 만듭니다."가 2번 출력되고 <__main__.Car object at 0x100f27fd0>와 같은 내용이 출력된다.
  - 이것은, 우리가 현실속에 있는 자동차 object, 객체를 python 코드로 클래스로 추상화해서 재정의를 했고 그렇게 만든 클래스를 기반으로 프로그래밍 세상에 자동차라는 object를 찍어 냈는데, 그렇게 만들어진 자동차 object, car object가 컴퓨터 메모리 상에 어딘가에 저장이 되어야 우리는 그걸 인스턴스라고 부르게 된다.
  - 그래서, 우리가 그냥 print(인스턴스이름) 이렇게 하면 -> 우리가 따로 수정하지 않는 한 기본적으로 해당 인스턴스가 컴퓨터 메모리 상에 어떤 메모리 주소에 위치하고 있는지가 터미널에 출력된다. 위 코드에서 0x100f27fd0는 컴퓨터 메모리의 주소를 가리키는 16진수 주소에 해당한다. 메모리 상에 주소를 기준으로 어떤 주소에 위치해 있는지를 표현해주는 것이다. 
  - 만약, 우리가 print(인스턴스이름)을 했을 때 그 인스턴스의 name, color 등 인스턴스 변수의 값들을 확인하고 싶다면

```python
class Car:
    def __init__(self, name, color, fuel):
        print("자동차를 만듭니다.")
        self.name = name
        self.color = color
        self.fuel = fuel
        self.mileage = 0

genesis = Car("GENESIS", "black", 50)
ferrari = Car("Ferrari", "Rosso Corsa", 40)

print(genesis.name, genesis.color, genesis.fuel, genesis.mileage)
print(ferrari.name, ferrari.color, ferrari.fuel, genesis.mileage)

>>>
자동차를 만듭니다.
자동차를 만듭니다.
GENESIS black 50 0
Ferrari Rosso Corsa 40 0
```

- 이런식으로 print하고 인스턴스이름.인스턴스변수 이렇게 .으로 인스턴스 내부에 접근해서 출력할 수 있다.


