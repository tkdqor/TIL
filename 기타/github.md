## Github이란
- Git으로 관리한 내용들을 Github을 사용하여 온라인에 업로드해서 보관하고 다른 사람과 협업하는 과정을 다뤄보자.
- Git에서 commit을 통해 -> 나의 프로젝트를 local 저장소에 반영했고 -> push를 통해 local 저장소의 내용을 원격 저장소에 반영하자.
- Git을 사용해서 클라우드를 쓰듯이 소스들을 업로드해서 저장하고 다른 개발자들과 공유할 수 있는 Git 저장소들 중, 대표적인 것으로 Bitbucket, Gitlab, Github 등이 있다.

* * * 

**1. Github 저장소 만들기**
- github 계정을 만들고 로그인 이후에 Create a new repository를 클릭하면 -> Git으로 관리되는 프로젝트 폴더 하나가 원격으로 저장되는 공간이 생성된다.
- 이 때, Public를 선택하면 Github에 접속한 모든 사람들이 내 코드를 방문하거나 검색할 수 있다. 
- Private은 나, 그리고 내가 이 레파지토리에 초대한 특정 사용자들만 볼 수 있다.
- 오픈소스로 만들어서 사람들에게 공유하고 도움이나 참여를 받으려면 혹은 기업들에게 공개할 내 포트폴리오로 사용하려면 Public을 선택하자.
- 해당 사항들을 선택하여 저장소를 생성.
  - Github repository의 url은 -> https://github.com/username/레파지토리명  이렇게 설정된다.

* * *

**2-1. Github에 소스 올리기**
- 이제 자신의 프로젝트를 새로 생성한 레파지토리에 올려보자.
```
git status
```
해당 명령어로 현재까지의 사항들이 모두 commit 되어있는지 확인하기. 여기서 빠진 게 있다면 전부 add해서 commit을 먼저 진행하자.
```
git remote
```
이 상태에서 위의 명령어를 입력하면 현 폴더의 원격 레파지토리를 확인할 수 있는데, 아직 설정한 게 없다면 아무것도 뜨지 않을 것이다.  
이제 github에서 만든 새로운 레파지토리를 원격 저장소로 추가해보자.
- https://www.yalco.kr/_02_github_token/  

해당 내용을 통해 먼저 Personal access token를 생성해주자. 그 이후에는
```
git remote add origin 새로만든github주소
```
이 명령어는 새로 만든 github 레파지토리를 본인의 local 프로젝트의 'origin'이란 이름의 원격 저장소로 설정하겠다는 의미이다.  
이 'origin'은 원하는 다른 이름으로 수정해도 되나 Git 초기화 시 기본 브랜치명이 master인 것처럼 흔히 기본값으로 사용되는 이름이라고 생각하면 된다.
```
git push -u origin master
```
이 push 명령어는 프로젝트 폴더의 현 브랜치에 commit된 내용들을 origin이라는 이름의 원격 레파지토리에 master라는 이름의 브랜치에 올리겠다는 의미이다.  
해당 명령어를 다 입력하고 나면
```
Branch 'master' set up to track remote branch 'master' from 'origin'.
```
다음과 같은 내용이 뜨면서 local 컴퓨터의 master branch가 origin이라는 원격 저장소의 master branch를 추적한다고 나온다.  
그리고나서 github에 가보면 -> local로부터 push한 파일들이 올라와 있고 / 각 파일들이 어떤 commit에서 마지막으로 생성되었거나 변경되었는지 나와있다.  
파일명을 클릭하면, 파일의 내용을 볼 수 있고 / commit 메시지를 클릭하면, 해당 commit에서 어떤 변화들이 있었던건지 확인할 수도 있다.
```
git remote
```
다시 입력해보면, 이제는 origin이라는 추가한 원격 레파지토리의 이름이 나오게 된다.


**2-2. 변화된 내용을 다시 push하기**
- 자신의 프로젝트에서 작업을 하고 변경된 내용을 github에 push해보자.
```
git add -A
git commit -m "새로운 파일 추가"
```
이렇게 변경된 파일을 local에서 추가하고 다시 commit한 다음
```
git push origin master
```
라고 입력해주면 github에 추가된 내용을 확인할 수 있다. 

* * * 

**3. 다루지 않을 파일 설정하기 - .gitignore**
- 프로젝트를 진행할 때, Git으로 관리하고 Github에 올릴 필요가 없거나 오히려 그래서는 안 되는 파일들이 있을 수 있다.
- 코드를 실행하면 패키지가 다운받아지거나 코드대로 빌드되어서 자동으로 생성되는 파일들은, 어차피 코드만 있으면 그 때마다 실행해서 만들 수 있기 때문에 굳이 Git에 무리를 주거나 Github 용량을 차지할 필요가 없다.
- 또한, 보안적으로 중요한 내용 / 예를 들어 데이터베이스 계정 등이 담긴 파일은 오픈소스 프로젝트라도 대중에게 공개되면 안 된다.
- 이럴 때 사용되는 게 바로 **.gitignore 파일** 이다.

1. 프로젝트 폴더 바로 안쪽, 최상위에 .gitignore라는 파일을 생성한다. 해당 파일은 숨김파일로 지정된다. 
  - https://www.atlassian.com/git/tutorials/saving-changes/gitignore 여기에서 Git 관리에서 제외시킬 파일이나 폴더, 기타 요소를 지정하는 형식이 나와있다.

2. 새롭게 파일을 추가하고 해당 파일의 이름을 .gitignore라는 파일안에 입력해준다.
   - ex) .gitignore 파일안에 ajajaja.py 이렇게 입력

3. 그리고나서
```
git status
```
라고 입력해봤을 때, 해당 파일은 commit 해야할 목록에서 없어진다. 이 상태에서
```
git add -A
git commit -m "gitignore사용"
```
이렇게 입력하고 
