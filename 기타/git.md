## Git 설치

- git은 Command line Interface로 명령어를 입력하는 방법과 마우스로 쓸 수 있게 GUI로 만들어진 소프트웨어(ex. Source Tree)를 쓰는 방법이 있다.
1. https://git-scm.com/ 해당 사이트에 접속해서 먼저 git을 설치.
2. IDE라고 불리는 통합 개발 환경을 조성해주는 소프트웨어를 사용 (ex. Vs code)
3. 마지막으로 마우스로 git을 다룰 수 있게 해주는 SourceTree 홈페이지로 가서 다운로드. https://www.sourcetreeapp.com/


## Git 사용법

**1. Git 저장소 만들기**   
- 터미널을 이용해서 현재 폴더로 이동한 후,
```
git init
```
다음과 같이 입력하면 빈 저장소가 만들어졌다는 메시지가 뜬다. 즉, 해당 폴더가 git의 관리 하에 들어간 것이다.  
그리고 나서는,
```
git config --global user.name "내 이름"
git config --global user.email "내 이메일 주소"
```
이렇게 입력해주면 된다. 그러면 해당 폴더안에 .git 이라는 숨겨진 폴더가 생성된다. 


**2. 현재 시점을 저장하기**
```
git status
```
- 해당 명령어로 현재 git에 담기지 않은 파일들을 확인할 수 있다. (이 파일들이 만들어졌다는 사실을 알 수 있다..?)

```
git add -A
```
- 이렇게 이어서 입력하면, 해당 폴더의 모든 것들을 git의 타임캡슐에 넣는 것이다.
- 이 상태에서 다시 git status를 입력하게 되면 Changes to be committed: 라는 메시지가 뜨게 되고 git에 같이 묻은 것들 이라고 이해하면 된다.

```
git commit -m "해당 커밋에 대한 설명 적기"
```
- 그리고 commit를 할 때 -m을 이용해서 해당 commit에 대한 설명을 적어주자.
- 이 상태에서 git status를 입력해보면 -> 담아서 묻을 게 없다고 나온다.
- 그리고 이 상태에서 다시  
  git add -A -> git commit -m "dd" 이렇게 입력하면 -> 변화된 파일들에 대한 commit을 추가로 진행해도 그 이전 작업들이 같이 이어져 온다고 생각하면 된다.

```
git log
```
- 이 명령어를 입력하면, 그동안의 commit 내역들을 확인해볼 수 있다.(캡슐에 담은 정보와 메시지를 확인 가능)
- 그리고 :q를 입력해서 해당 상태에서 빠져나올 수 있다.
