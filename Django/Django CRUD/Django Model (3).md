## 데이터베이스 id column
- 우리가 생성한 데이터베이스는 기본적으로 맨 앞에 id라는 column이 자동으로 추가된다. 이러한 id는 데이터를 구분하는 key가 된다.
  - 이 id column은 우리가 별도로 지정하지 않아도 알아서 자동으로 추가된다. 게시물이 추가될 때마다 그 값이 자동으로 증가한다.
  - 그리고 이 id로 우리가 찾고자 하는 데이터를 빠르게 찾아낼 수 있다.

- **RDB에서 데이터를 조회하기 위해 가장 사용하기 좋은 식별자를 Primary Key라고 부른다. 이 id가 Primary Key에 해당한다.**


## python 코드로 데이터 관리하기
```terminal
python manage.py shell
```

- **terminal에 위와같이 입력하면 -> django의 각종 기능을 테스트해볼 수 있는 환경이 열리는 django shell을 사용할 수 있다.**
  - 참고로 terminal에 python3 이렇게 입력해도 **REPL(레플)** 이라는 기능을 활용할 수 있다. >>>가 출력되면서 python 코드를 입력하고 계산 값을 바로 확인할 수 있다.
  - **django shell은 REPL과 유사하나, django의 설정 파일인 settings.py을 비롯해서 각종 django의 기능들을 활성화한 상태에서 실행되기 때문에, django에서만 사용가능한 다양한 리소스와 기능들을 사용할 수 
    있다는 점이 다르다.**
    
- django shell에서 Post model 클래스를 활용해 데이터를 직접 생성해보자.

```terminal
>>> from posts.models import Post
>>> Post.objects.all()
<QuerySet [<Post: 보라돌이: 보라보라: 2022-02-14 08:54:10.887019+00:00>, <Post: 뚜비: 뚜비뚜비: 2022-02-14 08:57:27.698818+00:00>, <Post: 나나: 나나나나: 2022-02-14 08:59:08.763715+00:00>, <Post: 뽀: 뽀뽀뽀뽀: 2022-02-14 08:59:15.172612+00:00>]>

```

- **모든 모델 클래스에는 objects라고 불리는 model manager라는 게 있다. 이 model manager 안에는 데이터베이스를 조작할 수 있는 각종 쿼리 기능들이 존재한다.**
  - Query는 해석하다, 질의하다의 뜻. 즉, 데이터베이스한테 데이터를 조회하고 생성하기 위해 요청하는 것이다.

- Post.objects.all() queryset은 -> 데이터베이스로부터 Post 테이블 내부에 있는 모든 데이터를 불러오는 하나의 기능이다.
- **QuerySet은 django manager를 통해서 데이터베이스로부터 가져온 데이터의 묶음을 의미한다. 데이터베이스 테이블에 저장되어 있었던 각각의 행, 각각의 row가 -> model 클래스의 인스턴스로 변환이 되어서 
  묶음으로 전달이 된 것이다.**
  
  
  ### python django shell로 게시물 생성
  
