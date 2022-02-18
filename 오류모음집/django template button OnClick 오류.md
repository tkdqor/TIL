## django template button OnClick 오류
- django template에서 button element를 사용하고 OnClick 속성을 주고 싶을 때, django template language로 url 설정을 하는데 있어 오류가 계속 발생했다.
  - 처음 구글링을 통해 시도해 본 코드는 다음과 같다.

```html
...
<button type="submit" class="btn btn-secondary btn-lg" onclick="location.href='{% url 'index' %}'">여행지 구경하기</button>
...
```

- 해당 코드로 실행했을 때, index라는 url을 호출하지 못했다. 다시 구글링을 한 결과, 아래와 같이 큰따옴표를 먼저 작성하는 방식, 기존의 django template language 사용방식과 동일하게 작성했을 때 오류가 나지 않는 것을 확인했다.

```html
...
<button type="submit" class="btn btn-secondary btn-lg" OnClick=location.href="{% url 'posts:index' %}">여행지 구경하기</button>
...
```





