## 알아야 할 사항
- ORM은 어디까지나 SQL 생성을 도와주는 라이브러리이다. ORM이 DB에 대한 모든 것을 알아서 처리해주지는 않는다.
- 보다 성능이 높은 애플리케이션을 만들고자 한다면, 사용하는 DB엔진과 SQL에 대한 높은 이해가 필요하다. 우리가 ORM을 통해서 직접 SQL를 작성하지는 않지만, ORM를 통해 생성된 SQL에 대해서 잘 이해하고 사용할 수 있어야 된다.


## RDBMS에서의 관계 예시(관계형 데이터베이스에만 있는 개념)
- **1:N 관계 : models.ForeignKey라고 표현** 
  - 1명의 유저(User)가 쓰는 다수의 포스팅(Post) / 그래서 User가 1, Post가 N이라고 표현. N의 모델에서 1에 해당하는 모델이 중복될 수 있다.
  - 1명의 유저(User)가 쓰는 다수의 댓글(Comment) 
  - 1개의 포스팅(Post)에 다수의 댓글(Comment)
  - ex) 1명의 User / 1개의 Post / 1개의 Comment가 있다고 가정했을 때, User와 Post간의 관계는 1:N 관계이고 / User와 Comment간의 관계도 1:N 관계이고 / Post와 Comment간의 관계도 1:N 관계이다.
    - 이러한 관계를 표현할 때는, 외래키(ForeingKey)라는 필드를 통해서 모델에서 표현할 수 있다. -> N측에다가 외래키를 심으면 된다.
    - User와 Post에서는 Post에다가 외래키를 정하고, Post와 Comment와의 관계는 Comment쪽에 정하고, User와 Comment관계에서는 Comment에다가 정하면 된다.


- **1:1 관계 : models.OneToOneField로 표현**
  - 1명의 유저(User)는 1개의 프로필(Profile)을 가지는 경우이다.
  - User와 Profile간의 관계는 1:1 관계이니까 이 관계를 User측에다가 정해도 되고 / Profile측에 이 관계를 명시해도 된다.
    - 그런데, django에서의 User 모델은 기본적으로 django.contrib.auth 앱에서 User 모델을 지원해준다. 그래서 우리가 정의하는 게 아니고, django가 정의한 User 모델이기 때문에 우리가 이 User 모델을 변경할 수 없다.
    - 물론 django에서는 커스텀 User 모델을 따로 만들어서 사용할 수 있는 설정이 있다. 하지만 그건 사용하는 User 모델의 클래스를 변경하는 것일 뿐, 해당 클래스 자체를 변경하는 건 아니다. 필드 자체를 바꾸는 건 아니다.
    - 그래서 User - Profile 처럼 1:1 관계는 둘 다 설정해도 되지만, Profile 측에다가 User에 대한 관계를 OneToOneField로서 정의한다.
