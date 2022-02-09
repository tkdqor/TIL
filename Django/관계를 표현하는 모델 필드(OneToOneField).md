## OneToOneField
- 1대1 관계에서 어느 쪽이라도 지정 가능
  - 예외적으로 User : Profile 이렇게 2개의 모델이 있을 때, User 모델은 django의 auth앱의 User 모델을 우리가 변경할 수 없기 때문에 Profile에 설정해야 함.

- ForeignKey(unique=True)와 유사하지만, reverse 차이
  - 외래키는 1:N인데 ForeignKey에 unique=True를 지정하면 OneToOneField와 유사하게 동작하지만, reverse name에서 차이가 나게 된다.
    - User:Profile를 FK로 지정한다면 -> 
