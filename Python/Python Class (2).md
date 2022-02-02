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
print()
print(ferrari.name, ferrari.color, ferrari.fuel, genesis.mileage)

>>>
자동차를 만듭니다.
자동차를 만듭니다.
GENESIS black 50 0
Ferrari Rosso Corsa 40 0
```

- 이런식으로 print하고 인스턴스이름.인스턴스변수 이렇게 .으로 인스턴스 내부에 접근해서 출력할 수 있다. 
- 또한, print() 이렇게만 입력하면 터미널에 공백 한 줄을 출력하게 된다.

- 이렇게 정보를 확인할 수 있지만 그래도 여전히 불편하다. print(인스턴스이름) 이렇게 했을 때 -> 우리가 커스텀할 수 있는 방법을 알아보자.


```python
class Car:
    def __init__(self, name, color, fuel):
        print("자동차를 만듭니다.")
        self.name = name
        self.color = color
        self.fuel = fuel
        self.mileage = 0

    def __str__(self):
        return f'{self.name} {self.color} {self.fuel} {self.mileage}'


genesis = Car("GENESIS", "black", 50)
ferrari = Car("Ferrari", "Rosso Corsa", 40)


print(genesis)
print(ferrari)

>>>
자동차를 만듭니다.
자동차를 만듭니다.
GENESIS black 50 0
Ferrari Rosso Corsa 40 0
```

- 이렇게 클래스 내부에 던더string 메소드를 정의하면 커스텀할 수 있다. 메소드 내부에는 문자열을 retur해주면 우리가 print(인스턴스 이름)을 했을 때 어떠한 문자열이 출력되기를 원하는지 설정할 수 있다.
  - fstring를 이용해서 위와 같이 설정해주고 print를 해보면 우리가 설정한 대로 출력이 된다.


```python
class Car:
    def __init__(self, name, color, fuel):
        print("자동차를 만듭니다.")
        self.name = name
        self.color = color
        self.fuel = fuel
        self.mileage = 0


    def accel(self):
        self.fuel -= 2
        self.mileage += 10


    def __str__(self):
        return f'{self.name} {self.color} {self.fuel} {self.mileage}'


genesis = Car("GENESIS", "black", 50)
ferrari = Car("Ferrari", "Rosso Corsa", 40)


print(genesis)
print(ferrari)
print()
genesis.accel()
ferrari.accel()
print(genesis)
print(ferrari)

>>>
자동차를 만듭니다.
자동차를 만듭니다.
GENESIS black 50 0
Ferrari Rosso Corsa 40 0

GENESIS black 48 10
Ferrari Rosso Corsa 38 10
```

- 이번에는 클래스 내부에 accel이라는 메소드를 새롭게 추가했다. 이 메소드에서 self.fuel / self.mileage와 같이 인스턴스에 저장되어 있는 값에 접근하게 되는데, 애초에 이 accel이라는 메소드가 호출되는 과정이, 우리가 genesis와 ferrari라는 인스턴스를 만들고 .점을 기준으로 genesis와 ferrari 인스턴스 내부에 있는 변수에 접근을 했었다. 그래서 이 genesis가 굴러가게끔 하려면 genesis.accel 이렇게 사용한다. **따라서 accel이라는 메소드는 우리가 생성한 인스턴스와 관련되어 있기 때문에 그 인스턴스가 보유하고 있는 accel이 실행된다고 생각할 수 있다.** genesis.accel() 이렇게 코드를 작성하면 genesis 인스턴스 내부에 accel이 수행된다고 볼 수 있다.
- def accel(self): -> 이렇게 정의했을 때 self에 우리가 생성한 genesis나 ferrari 인스턴스에 대한 정보가 담겨있는 것이다.
- 위와 같이 genesis.accel() / ferrari.accel() 이렇게 메소드를 호출한 이후에는 -> 인스턴스변수인 fuel과 mileage에 변동이 생긴 걸 확인할 수 있다.



## 클래스 변수란
- 지금까지는 클래스 내부의 메소드에서 설정한 인스턴스 변수만 사용했었는데, 인스턴스와 별개로 인스턴스가 없어도 상관이 없고 언제든 사용할 수 있는 클래스 변수라는 것이 존재한다. 일반적으로 인스턴스 전반에 걸쳐 사용하거나 파악해야 하는 값이 있을 때 클래스 변수를 사용하게 된다.

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


# 클래스 변수를 활용해서 생성된 자동차 인스턴스 출력
print(f'생성된 자동차 수: {Car.count}')

genesis = Car("GENESIS", "black", 50)
ferrari = Car("Ferrari", "Rosso Corsa", 40)

# 클래스 변수를 활용해서 생성된 자동차 인스턴스 출력
print(f'생성된 자동차 수: {Car.count}')

print(genesis)
print(ferrari)
print()
genesis.accel()
ferrari.accel()
print(genesis)
print(ferrari)

>>>
생성된 자동차 수: 0
생성된 자동차 수: 2
GENESIS black 50 0
Ferrari Rosso Corsa 40 0

GENESIS black 48 10
Ferrari Rosso Corsa 38 10

```

  - Car 클래스로 지금까지 총 몇 대의 인스턴스가 만들어졌는지 파악하기 위해 클래스 정의 바로 밑에 count = 0과 같이, 클래스 변수를 설정할 수 있다. 그래서 던더init 메소드에 Car.count += 1 이렇게해서 자동차가 만들어질 때마다(Car 인스턴스가 하나 만들어질 때마다) 클래스 변수 count를 1만큼 증가시켜주자라고 설정한 것이다. 인스턴스 변수가 아니기 때문에 self. 가 아닌 **클래스이름.클래스변수** 와 같은 형태로 접근하자.
  - 마지막에 print(f"Car count: {Car.count}")로 클래스 변수를 출력해서 확인할 수 있다. 
