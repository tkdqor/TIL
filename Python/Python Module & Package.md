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
