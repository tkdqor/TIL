## QuerySet API로 Model 수정 및 삭제
- 먼저, 이전까지 작업했던 liongram 디렉터리 - posts 디렉터리 - models.py를 다시 확인해보자.

- **그리고, python 인터프리터의 경로를 직접 찾아서 확인해볼 수 있다.**
  - 루트 디렉터리 내부에 venv 디렉터리 -> bin 디렉터리 -> python 파일을 선택해주면 된다. (맥북은 python3 일 것이다)

- **그 다음, liongram 위치에서 django shell를 들어가보자.**

```python
python manage.py shell
```

- **사실 이 터미널에서 그냥 python이라고만 입력해도 python 인터프리터가 실행되지만, 그렇게 해서는 from models 이렇게 사용할 수 없다. 위의 코드를 입력해서 인터프리터를 실행하면 django 변수들을 가져와서 실행하기 때문에 from posts.models 이렇게 사용할 수 있다.**

