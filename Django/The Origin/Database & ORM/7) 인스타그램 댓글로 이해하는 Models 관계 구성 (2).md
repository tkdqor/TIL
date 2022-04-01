## 인스타그램 댓글로 이해하는 Models 관계 구성 (2)


### 댓글 모델 코드 작성하기
- **먼저 VSCode에서 python 인터프리터 설정을 먼저 해보자.**
- VSCode 위쪽에 보기 클릭 -> 명령 팔레트 클릭 -> 여기서 inter라고만 쳐도 Python Select Interpreter라고 나오고 이걸 클릭하자. 그 다음에는 우리가 만들었던 가상환경인 venv의 bin/python
  이걸 찾아서 클릭해주면 된다. 없으면 직접 찾아가서 들어가자. bin으로 들어가서 python.exe를 클릭해준다. 
  - **이걸 설정하는 이유는, 지금 VSCode가 어떤 python를 사용하라고 우리가 지정해준 것이다.** 그래야지 command를 누르고 클릭했을 때 이동이 된다든지, django를 제대로 인식하게 된다. 꼭 이 과정을
    진행해줘야 한다.
    
- 이제 댓글 모델을 만들어주자. models.py로 가서 설정하기
```python
...
class Comment(models.Model):





- **class Comment(models.Model)**: --> **이 부분은 python은 객체지향 프로그래밍 언어이기 때문에 클래스라는 개념이 존재하게 되는데, 여기서 models.Model 이렇게 넣어주게 되면 --> Model이라는
  클래스를 상속받아서 Comment라는 클래스를 정의해주었다고 보면 된다.**
  - **해당 Model이라고 된 부분을 command로 들어가보면, class Model(metaclass=ModelBase): 이렇게 정의되어있다. 그리고 이 Model이라는 클래스 내부에 from_db 등 정말 많은 함수들이 정의되어있다. 이러한
    것들이 django에 이미 생성되어있는 것이기 때문에 우리가 이걸 가져다가 쓰면 이게 나중에 데이터베이스 테이블로 생성이 되는 일들을 사용할 수 있는 것이다.**
