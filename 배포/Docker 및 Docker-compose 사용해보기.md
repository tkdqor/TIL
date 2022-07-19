## Docker로 django와 nginx 띄우기
- [참고 영상](https://www.youtube.com/watch?v=GpvfIs_ewGY)
- [참고 블로그](https://cholol.tistory.com/489)

- **Docker로 진행하게 되면, aws ec2에 직접 django와 nginx를 설치하는 것이 아니라, docker를 이용해 그 안에 django 컨테이너와 nginx 컨테이너를 만들고 이미지를 빌드해서 올리게 된다.**
  - **그리고 django 컨테이너와 nginx 컨테이너를 동시에 관리하기 위해서 docker-compose 라는 걸 사용해볼 수 있다.**
  - 이렇게 진행하면 aws ec2 우분투가 아닌 다른 서버에서도 손쉽게 설치를 진행할 수 있게 된다.

<br>

### django 컨테이너 및 이미지 생성하기
- 처음에 ec2에서 mkdir docker-server 명령어로 docker-server라는 폴더를 생성한다. cd를 입력하고 해당 폴더에 들어가면, 그 위치가 앞으로 docker-compose의 루트가 될 경로이다. 해당 위치에서 django와 nginx 컨테이너를 만들 예정이다.
