## Mixin
- Mixin은 하나의 Generic View 클래스와 다른 Generic View 클래스의 특성을 같이 조합해서 쓰고 싶을 때 사용하는 개념이다. 그냥 봤을 때는 다중상속처럼 보이지만, 객체지향 패턴에 믹스인 패턴에서 가져온 개념이기
  때문에, 엄밀히 말해서는 어떤 Generic View 클래스를 상속해서 클래스 형태의 View를 구현할 때 다른 Generic View의 특성을 포함하도록 하는 개념이다.
- 그러니까 상속이 아니라, 포함으로 해석할 수 있는 디자인 패턴이다. A라는 클래스를 상속하면서 동시에 B라는 클래스의 특성만 상속하는 것이다. django에서는 이러한 패턴을 지원하기 위해서 여러 Mixin 클래스들을 지원한다.
  - Mixin 기법을 쓰고 싶다면 선언문에 MixinClass와 일반 View Class를 상속받는 형태로 선언을 해야한다.

- 대표적인 Mixin클래스들로는 다음과 같다.
  - TemplateResponseMixin은 TemplateView의 특성을 가지고 있고, SingleObjectMixin은 DetailView / MultipleObjectMixin은 ListView / FormMixin은 FormView / ModelFormMixin은 CreateView와 UpdateView의 특성을 가지고 있다.
  - 이외에 Mixin은 공식문서에서 확인가능하다.
  - 우리는 SingleObjectMixin과 ListView를 결합해서 이전에 만들었던 TaskdetailView를 수정해보도록 하자.
