## Django Models 이해하기
- models.py가 model 역할을 해준다.
- **요즘에는 인터넷 웹서비스가 기반이기 때문에 DB를 이용해서 데이터를 저장하는 게 무조건 필수가 되었다. 그리고 최근에는 클라우드에 저장하는 게 당연히 되고 있다.**
- 이번에는 클라이언트 요청 이후 -> urls.py에서 받고 -> views.py에서 models.py를 이용해 데이터를 가져와서 응답을 해보자.


### Model의 역할
- 데이터를 관리하는 역할
- 데이터베이스에 저장할 테이블 정의
- 모델에 작성된 코드를 기준으로 데이터베이스 생성
  - 우리가 models.py에 입력하는 python 코드를 ORM이 해석해서 데이터베이스를 생성해준다.


### Model의 주요 필드
- CharField : 자료형은 str()로 작거나 큰 문자열을 위한 문자열 필드 / max_length라는 속성을 가진다.
- TextField : 자료형은 str()로 긴 텍스트 필드 
  - **CharField vs TextField --> 일반적으로 CharField를 사용한다고 한다면 사이즈가 몇인지 정해줘야한다. 반면에 TextField의 경우에는 크기를 정하지 않고 그냥 엄청 크게 잡아놓는다. 그래서 CharField는 제목을 주로 사용하고 TextField는 내용으로 사용하게 된다.**
- BooleanField : bool() 자료형으로 참/거짓 필드 
- DateTimeField : datetime() 자료형으로 날짜 및 시간 필드 / auto_now, auto_now_add 속성을 가지고 있다.
- IntegerField : int() 자료형으로 정수 필드
- FloatField : float() 자료형으로 부동 소수점 숫자 필드 


### Model 필드 주요 속성
- 모든 필드에 정의할 수 있는 속성
  - null : 값은 True/False로 None를 허용 여부. 데이터베이스의 데이터, 테이블의 특정 column이 null로 들어갈 수 있는지 여부.
  - blank : 값은 True/False로 공백을 허용 여부
    - **데이터가 None인 것과 vs 데이터가 ''이렇게 콤마콤마로 들어가는 건 비어있는 것으로 다르게 본다.** 
  - choices : 값은 list(tuple())로 선택적인 값 입력
    - **특정 column의 필드는 a,b,c라는 것 중에 선택을 해야된다면 또는 회원가입 시 성별을 선택하게 한다면 choices 속성을 정의해주면 된다.**
  - default : 값은 필드의 자료형 값으로 기본적인 값을 지정
  - **unique : 값은 True/False로 해당 모델간의 고유한 값의 여부**
    - ex) 주민등록번호, 사용자 ID 등등
  - verbose_name : 값은 str()로 사용자에게 노출될 문구
    - 관리자 페이지와 관련있음
- CharField에 정의할 수 있는 속성
  - max_length : int()값으로 최대 글자 수의 제한 
