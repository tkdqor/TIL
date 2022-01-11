# Github push 오류
- git push를 했을 때, 
```python 
! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/tkdqor/likelion'
```
다음과 같은 에러가 나는 경우이다.

* * *

참고 : https://sosoeasy.tistory.com/406
- 구글링을 통해 확인한 결과, 해당 오류는 원격 저장소에 내 local에는 없는 파일이 있을 때 내 파일들을 push하면 발생하는 오류라고 한다.
- 생각해보니 내가 local 프로젝트 안에다가 README.md 파일을 만들지 않고 github에서만 작성해서 해당 오류가 발생한 것 같다.

* * *
- 해당 오류는 원격 저장소에서 내 local에 저장하지 않은 파일을 pull한 이후, 다시 원격 저장소에 push를 하면 된다.
```
git pull origin master
git push origin master
```
이렇게 입력하면 github에 push가 정상적으로 진행된다!
