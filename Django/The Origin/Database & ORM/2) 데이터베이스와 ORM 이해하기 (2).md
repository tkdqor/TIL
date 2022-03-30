## 데이터베이스와 ORM 이해하기 (2)


### django User model
- django 내부에 생성되어있는 User 모델은 class AbstractUser(AbstractBaseUser, PermissionsMixin): 이렇게 시작된다.
- django User 모델은 id, password, last_login, is_superuser, username, last_name, email, is_staff, is_active, date_joined, first_name 이러한 필드들을 가지고 있다.
  - password는 해싱되서 들어가게 된다.
  - username이 사용자 ID라고 생각하면 된다. 
  - is_staff는 관리자 사이트에 들어갈 수 있는지 여부이다. 그리고 is_active는 로그인 자체 기능이 활성화되는지 여부이다.

- 그래서 User 모델의 코드는 **객체**이다. python이 객체 지향 언어이기 때문에 클래스로 정의가 되어있고 나중에 사용할 때는 객체로 사용하게 된다. / 반면에 User 모델의 테이블은 **관계형 데이터베이스**이기
  때문에 객체가 아니다.
  - **그래서 이 2개에 대한 차이가 발생하게 된다. "객체지향언어 != 관계형 데이터베이스"
  - 객체지향 언어에서는 데이터베이스에 있는 데이터들을 가지고와서 프로그래밍적으로 사용하는 것이지 관게형 데이터베이스와는 다르다.

- **그래서 필요한게 바로 ORM이다.**
  - **ORM은 python의 객체와 - 데이터베이스의 데이터를 --> 연결해주는 역할을 해준다.**
  - ORM은 Object Relational Mapping이라고 해서 객체와 관계를 매핑해주는 기술이다. 프로그래밍에 다 있는 기술, 소프트웨어이다. django도 django ORM이 따로 있는 것이다.
  - 이렇게 매핑을 해야하는 이유는, 객체지향언어와 관계형 데이터베이스가 전혀 다르고 다른 목적으로 생겨났고, 다르게 설계 및 구성이 되어있기 때문에 -> 이 2개를 연결시켜주기 위한 도구이다.
    - User 모델이 있을 때, user가 갖고 있는 게시글들을 보고 싶을 수 있다. 근데 DB를 보면 User 테이블이 있고 게시글 테이블이 따로 있게 된다. / 반면에 코드로 보면 user가 게시글 데이터를 가지고
      있게 된다. 그래서 이럴 때 완전 다르다는 느낌을 받을 수 있다.
