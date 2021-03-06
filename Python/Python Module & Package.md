## Module
- 특정 기능에 대해 오랜 기간 개발을 하게 된다면, 파일 하나의 코드가 엄청나게 늘어나게 된다. 따라서 이러한 파일들을 분류해놓으면 찾기도 쉽고 수정하기도 편리해진다.
- Python 모듈이란, python 변수 / 함수 / 클래스 등을 관련 있는 것들끼리 모아놓은 하나의 python 파일이다.
  - 따라서 모든 python 파일은 하나의 python 모듈이라고 할 수 있다. 이렇게 분류를 함으로서 해당 기능이 필요한 다른 파일에서 이 모듈을 가져다가 재활용할 수 있다.

- python에는 이미 내장된 여러가지 모듈이 있는데, random 모듈을 사용해보자. random 모듈 안에는 무작위한 것들과 관련된 다양한 함수가 작성되어 있다.
  - 즉, 어딘가에 random.py라는 이름을 가진 python 파일이 있다는 의미이다.

```python
import random

numbers = [1, 2, 3, 4, 5]
rand_numbers = random.sample(numbers, 3)
print(rand_numbers)
```

- import라는 단어는 수입하다, 불러오다, 가져오다의 의미로 import random 하게 되면 -> 내가 random.py 안에 있는 내용들을 내가 사용하고자 불러오겠다는 의미이다.
  - **그 random.py 파일 내에 있는 코드들이 random 이라는 이름에 전부 저장되어 있기 때문에 -> 우리는 이 이후의 코드에서 random.~ 이러한 형태로 사용해야 한다.**

- 위와 같이 random 모듈 내에 있는 함수 중, sample이라는 함수를 사용했기 때문에 -> random.sample() 이러한 형태로 사용했다.
  - **즉, random.py 파일 내부에 def sample ~ 이렇게 이름이 sample인 함수가 정의되어 있다는 것이다.**

- python의 sample 함수는 데이터를 sampling 하고자 사용할 수 있는 함수이다. sample 함수의 첫번째 인자로는 샘플링 하고자 하는 대상, 데이터의 시퀀스를 입력하고 / 두번째 인자로는 그 대상 중에서 몇 개를 샘플링 할 것인지 개수를 입력하면 된다. 그리고 그 결과를 새로운 리스트로 만들어서 return 해준다. return를 해주기 때문에 rand_numbers라는 변수에 저장할 수 있었다.

- 이렇게, 우리는 이미 만들어진 python 파일들을 import해서 그 안에 있는 다양한 기능들을 사용할 수 있다. 실제로 개발자들이 뭔가를 전부 직접 다 개발하는 일은 오히려 드물다. 이미 많은 기능들이 언어 자체 레벨에서 혹은 라이브러리 레벨에서 다 작성되어 있다. 현업 개발자들은 문제를 해결하는데 가장 적합한 도구를 조사하고 또 이를 비교 분석해서 가장 효율적인 도구로 문제를 해결하는 사람들이다. 그 과정에서 마땅한 도구가 없다면 그 때 가서야 마땅한 도구가 없으니까, 그 도구를 직접 만드는 사람들이 바로 개발자라고 보면 된다.
  - **라이브러리란, 필요한 코드를 재사용하기 위해 언제든지 호출할 수 있도록 이미 만들어진 모듈과 패키지 등을 묶은 것을 의미.**


### 로또 번호 추첨기 만들기
```python
import random

lotto = list(range(1, 46))

lotto_numbers = random.sample(lotto, 6)
lotto_numbers.sort()

print(lotto_numbers)
```

- lotto라는 변수에 1부터 45까지 들어있는 리스트를 저장하고, 해당 리스트를 sample 함수를 이용해 무작위로 6개를 추출한다. 그리고 sort() 함수로 오름차순 정렬을 한 뒤 출력하는 과정이다.

```python
print(sorted(random.sample(range(1, 46), 6)))
```

- 또는 이렇게 한 줄로도 작성할 수 있다. sort() 함수는 오름차순 정렬만 할 뿐, 별도로 return 해주는 함수가 아니었지만 sorted() 함수는 기존에 있는 리스트를 건드리지 않고, 기존 리스트를 정렬한다면 어떻게 될지를 계산해서 그 계산된 값을 return 해주는 함수이다.


## 모듈이 아닌 함수를 import하기 (from 모듈명 import 함수명)
- 우리가 임의로 greetings.py라는 파일을 만들고 그 안에 함수를 정의해보자.
```python
def hello_sangbaek():
    print('Hello SANGBAEK!')

def hello_likelion():    
    print('Hello LIKELION!')
```

- 그 다음엔, use_greetings.py이라는 파일을 하나 더 만들고, greetings 모듈의 함수를 import 해보자.
```python
from greetings import hello_sangbaek, hello_likelion

hello_sangbaek()
hello_likelion()
```

- **이렇게 모듈이 아닌, 함수 또는 클래스를 import 하면 -> 해당 함수 또는 클래스를 파일 내부에서 사용할 때 모듈.함수() 이러한 형태가 아니라 함수() / 클래스 이렇게 간단하게 사용할 수 있게 된다.**

- 또한, 우리가 import 모듈명 이렇게 적어주었을 때, python 코드는 이걸 먼저 이 이름을 가진 모듈이 python 자체적으로 내장되어 있는 모듈인지를 검사해서 내장되어 있는 모듈이라면, 즉 빌트인 모듈이라면 그걸 사용하게 된다. 그런데 만약 빌트인 모듈이 아닌 이름을 작성했다면, python 코드는 '이 사람이 직접 만든건가보다' 해서 이 파일이 실행되는 위치에서(import한 파일 위치), 즉 이 파일이 실행되고 있는 현재 디렉터리에서 우리가 새롭게 작성한 모듈의 이름이 있는지 없는지 따져보고, 있으면 가져와서 쓰고 없으면 모듈 못 찾겠다고 에러를 발생시킨다.
  - **그리고 현재 디렉터리 외에도 PYTHONPATH라는 환경변수(시스템 소프트웨어의 동작에 영향을 미치는 값들의 모임)를 참조한다든지, 설치할 때 지정한 경로를 참조한다든지 하는 방법이 존재한다.**

* * *
## 패키지(Package)
- 우리가 만든 모듈, 혹은 남이 만든 모듈을 사용할 수 있다는 것을 배웠다. 하지만 시간이 지날수록 이 모듈의 크기와 양이 굉장히 많아질 수 밖에 없었다.
- 그래서 이 python 파일들, 모듈들도 관련있는 것들끼리 묶어서 관리할 수 있게끔 해주는 개념이 바로 python Package이다.
  - python 모듈들을 비슷하거나 관련있는 것들끼리 모아서 관리하기 위해 디렉터리 단위로 묶어서 관리하기 위해 사용하는 개념이다.

- ex) cars라는 패키지안에 -> car.py / ferrari.py / lamborghini.py 이렇게 cars와 관련된 모듈들이 모이게 된다. 실제로 이러한 구조를 실현해보면,

<img src="https://user-images.githubusercontent.com/95380638/152492707-9f958a1e-28dd-4d17-a1c5-aa53ef888f39.png" width="30%" height="30%">

- 이렇게 cars라는 이름을 가진 디렉터리를 만들고, 그 안에 car 모듈 / 페라리 모듈 / 람보르기니 모듈 이렇게 넣으면 -> python 패키지 구성이 모두 끝난다.
  - 그런데 뜬금없이 **던더init던더.py** 라는 이름을 가진 모듈이 왜 있는걸까? 
  - python 3.3버전 전에는 python package를 만들려면 반드시 이 던더init던더.py 라는 이름의 모듈을 디렉터리 안에다가 넣어줘야만 python 패키지를 만들수가 있었다. 그런데 지금은 최신 버전의 python를 사용하고 있기 때문에, 해당 이름을 가진 모듈을 굳이 넣어주지 않아도 python 패키지로서 동작하게 되지만 -> python 하위 버전에 대한 호환을 위해서 이 이름을 가진 파일을 넣어주는 게 일반적인 절차이다. 그래서 지금도 python 패키지를 만들 때에는 디렉터리 내부에, 이름이 던더init던더.py 이라는 파일을 넣어주면 된다.

```python
import cars.car                            # cars라는 패키지안에 있는 car 모듈
from cars import ferrari                   # cars라는 패키지안에 있는 ferrari 모듈
from cars.lamborghini import Lamborghini   # cars라는 패키지안에 lamborghini라는 모듈안에 있는 Lamborghini 클래스

normal_car = cars.car.Car('GENESIS', 'black', 50)   # cars라는 패키지안에 car라는 모듈안에 있는 Car 클래스를 사용 -> import한 cars.car까지 작성하고 그 뒤를 이어서 작성해야 한다. 
ferrari_car = ferrari.Ferrari()                     # ferrari 모듈을 import 했으니까 그 안에 있는 클래스를 사용하려면 -> ferrari.Ferrari() 이렇게 작성
Lamborghini_car = Lamborghini()                     # Lamborghini라는 클래스를 import 했으니까 -> 그대로 사용하면 된다.
```

- 그래서 이렇게 모듈이 패키지 내부에 구성되어 있을 때 즉, 예를 들어 car 모듈 / ferrari 모듈 / lamborghini 모듈이 cars라는 패키지 내부에 구성되어 있을 때,
  - import 하나만 사용할 경우에는 -> **import 패키지명.모듈명** 이렇게 패키지 이름을 적은 다음에 점을 찍고 패키지 내부에 있는 모듈 이름을 적어주면 된다.
  - 두번째로는 **from 패키지명 import 모듈명** 이렇게 사용할 수 있다.
  - 세번째로는 **from 패키지명.모듈명 import 함수명 or 클래스명** 이렇게 사용할 수 있다.
