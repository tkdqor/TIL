# Design Pattern

📖 **Contents**

- Design Pattern
  - [python에서 싱글턴 패턴 구현하기](#python에서-싱글턴-패턴-구현하기)

* * *

## python에서 싱글턴 패턴 구현하기
- **싱글턴(singleton) 패턴은 사용자가 여러 번 객체 생성을 하더라도 클래스로부터 오직 하나의 객체만 생성되도록 하는 디자인 패턴이다.** 이러한 싱글턴 패턴은 오직 유일한 객체를 통해서만 어떤 리소스에 접근해야하는 제약이 있는 상황에서 유용하게 사용할 수 있다. 클래스를 사용하는 입장에서는 실수로 여러 번 객체 생성을 시도하더라도 내부적으로는 오직 하나의 객체만 생성되고 사용된다.

- 유일한 객체가 생성되도록 해주는 싱글톤 패턴을 구현하기 위해서는 **\_\_new\__ 메서드를 재정의**해준 후 해당 메서드 내에서 이미 객체가 생성됐다면 객체를 생성해주지 않도록만 처리해주면 된다. 객체의 생성 여부는 모든 클래스 객체의 속성 변수로 \_instance를 추가한 후 해당 변수로 관리한다.
  - 그리고, **생성된 객체를 초기화하는 \_\_init\__** 도 여러번 호출되지 않도록 클래스 객체에 \_init 변수를 추가하고 이를 통해 이미 호출된 경우에는 호출되지 않도록 설정해준다.
  - [던더메소드 관련 내용](https://github.com/tkdqor/TIL/blob/main/%EA%B8%B0%EC%88%A0%20%EB%A9%B4%EC%A0%91%20%EB%8C%80%EB%B9%84%20%EA%B8%B0%EB%B3%B8%20%EA%B0%9C%EB%85%90%20%EC%A0%95%EB%A6%AC/Python.md#%EB%8D%98%EB%8D%94new%EB%8D%98%EB%8D%94-%EB%B3%80%EC%88%98%EC%99%80-%EB%8D%98%EB%8D%94init%EB%8D%98%EB%8D%94-%ED%95%A8%EC%88%98%EC%9D%98-%EC%B0%A8%EC%9D%B4)

```python
class Singleton(object):
    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, "_instance"):         # 정의한 클래스 객체에 _instance 속성이 없다면
            print("__new__ is called\n")
            cls._instance = super().__new__(cls)  # 정의한 클래스의 객체를 생성
        return cls._instance                      # 클래스._instance를 리턴

    def __init__(self, data):
        cls = type(self)
        if not hasattr(cls, "_init"):             # 정의한 클래스 객체에 _init 속성이 없다면
            print("__init__ is called\n")
            self.data = data
            cls._init = True                      # 클래스._init를 생성


s1 = Singleton(3)
s2 = Singleton(4)
print(s1.data)
print(s2.data)
```
```terminal
__new__ is called

__init__ is called

3
3
```

- [참고 사이트](https://wikidocs.net/69361)
