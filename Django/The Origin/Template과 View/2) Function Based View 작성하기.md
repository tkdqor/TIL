## Function Based View
- liongram 프로젝트 디렉터리를 열어주자.
- 지금 내가 사용하고 있는 python interpreter가 어디에 있는지 확인하려면 터미널에

```terminal
python

>>> import sys
>>> sys.executable
'/Users/kimsangbaek/Desktop/Mission/Challenge/first-django/venv/bin/python'

```

- 이렇게 python 실행시키고 sys를 import한 다음, executable 속성을 입력해보자. 그러면 지금 선택된 인터프리터의 경로가 나오게 된다.

- 인터프리터 설정은, VSCode에서 View -> ... -> interpreter를 입력하면 나오는 버튼 클릭 -> Find -> 현재 프로젝트 내부에 있는 venv -> 그 다음 bin 내부에 있는 python를 선택해주면 된다.
