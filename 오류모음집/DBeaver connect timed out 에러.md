## DBeaver connect timed out 에러
- AWS로 연결한 MySQL이 DBeaver에서 connect timed out 에러가 발생
- https://stackoverflow.com/questions/9500803/cant-connect-to-mysql-remote 
  - 해당 답변에서 connect timed out은 server가 busy하거나 방화벽 문제 둘 중 한가지 원인이라는 것을 확인.

- 그래서, AWS Console의 DB 인스턴스 인바운드 규칙에 설정된 내용을 전부 삭제한 뒤 다시 재설정하고 DBeaver에 연결했더니 성공

* * *
- 데이터베이스를 잠시 연결하지 않으면 끊어지는 상황을 방지하기 위해, 작업을 하지 않아도 연결이 되게끔 DBeaver의 Keep-Alive을 120으로 설정
  - https://www.codingfactory.net/12934

