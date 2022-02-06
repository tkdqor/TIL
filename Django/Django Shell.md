## Python의 Interactive shell
- shell은 python 코드를 한 줄 입력받고 그 코드에 대한 실행결과를 그 때 그 때 출력해주는 프로그램을 의미한다.
   - 기본 python shell은 -> terminal에 python만 입력하면 사용가능하다.

- IPython은 기본 python 패키지에는 없고 추가로 설치해야한다.
- Jupyter Notebook은 IPython에 대한 웹 버전이다. 서버를 띄우고 웹 브라우저를 통해서 iPython을 쓸 수 있다. 텍스트 이외 이미지 등 UI를 output으로 볼 수 있다. 그래서 데이터 분석이나 머신러닝에 필요하다.
  - 기본 shell에서는 텍스트만 볼 수 있는 것으로 다르다.


## 기본 python shell / django 프로젝트 설정이 로딩된 shell
- 기본 파이썬 쉘로는 바로 장고 프로젝트의 리소스 (모델, 템플릿 등)를 활용할 수 없다. 왜냐하면 장고 프로젝트 로딩 과정을 거치지 않았기 때문이다. python 입력하고 모델 데이터를 가져오려고 하면 오류가 난다. 즉 현재 python 프로젝트에 대한 로딩이 되어있지 않는 것이다.
- 그래서, 항상 shell를 띄울 때에는 
```terminal
python manage.py shell
```

- 이렇게 python django 환경이 로딩된 shell를 위의 코드로 띄워야 한다. 만약 여러 shell이 설치가 되어있다면, ipython shell을 먼저 검사하고 -> 그 다음에 bpython shell -> 그 다음에 python shell 이렇게 검사해서 먼저 있는 shell를 우선적으로 띄우게 된다.
  - 만약 아나콘다 환경이라면 conda install ipython / conda install jupyter 이렇게 설치를 진행하면 된다. 
- python manage.py shell 이라고 입력하면 -> 장고 환경이 로딩이 되어있기 때문에 

```terminal
>>> from instagram.models import Post
>>> Post.objects.all()
<QuerySet [<Post: 첫번째 메세지>, <Post: 두번째 메세지>, <Post: 세번째 메세지>]>
```

- 이렇게 모델에 접근할 수 있게 된다.

### --command 옵션 활용
```terminal
python -c "print(2**100)"
1267650600228229401496703205376

python manage.py shell -c "print(2**100)"
1267650600228229401496703205376
```

- python shell를 띄울 때 -c 입력하고 문자열을 같이 입력하면, shell로 진입안하고 바로 python 코드를 실행시켜준다. manage.py shell에서도 동일하게 사용 가능하다.
