## django Template 데이터 활용하기
- view 함수에서 render 함수의 세번째 인자로 HTML에서 표현하고자 하는 데이터를 담고있는 딕셔너리인 context를 전달하면, 해당 데이터가 template으로 전달되어 데이터를 사용할 수 있게 된다.
  - 딕셔너리 내부의 'key'가 변수로 활용된다.
- 이 변수를 html 내부에서 출력하고자 한다면, 중괄호를 2번 겹쳐서 그 안에 변수를 넣어주면 된다.

```python
def index(request):
    context = {'post': '나의 첫 django project...'}
    return render(request, 'posts/index.html', context)
```

```python
 <ul>
      <li>{{ post }}</li>
 </ul>
```

- 이렇게 views.py와 html 파일 설정을 할 수 있다.

- 우리가 django template language를 기반으로 template를 작성하면, django template engine이라는 것이 이 template 내부의 코드를 계산해서 최종적으로는 순수한 HTML 코드로 변환시켜준다.
  - django template engine이 {{ post }} 라는 코드를 읽으면 context 내부에서 post라는 데이터를 찾아서 value값을 활용하여 HTML 코드로 변환시키는 것이다.
