## 인스타그램 댓글로 이해하는 Models 관계 구성 (1)


### 모델 관계 설정
- 크게 3가지로 구성되어있다.
- **1:1관계 / 1:N관계 / M:N관계**


### 1:1관계
- 1:1관계의 경우에는 사용자가 회원가입해서 django의 User모델에 정보가 있으나, 선택적으로 입력하는 정보들이 있는 경우에 Detail이나 Info와 같은 추가적인 모델 테이블을 만들어서 User 모델과 1:1관계를 구성하는 경우가 있다.


### 1:N관계
- 이 경우에는 1개의 게시글과 그 게시글에 달린 댓글 여러 개를 예시로 생각해볼 수 있다. 1개의 게시글은 여러개의 댓글이 붙을 수 있고 / 1개의 댓글은 1개의 게시글에만 붙을 수 있다. 제일 많이 사용되는 관계이다.
- **1:N관계에서는 --> N이 되는 모델에 ForeignKey 필드를 사용해야 한다.**
  - **Post모델과 Comment모델이 있을 때는 Comment모델인 댓글쪽에 ForeignKey 필드를 설정해서 게시글을 참조하게 된다.**
  - **그래서 Post모델은 이미지/조회수/좋아요 사용자/내용/댓글 이렇게 필드를 구성하고, Comment모델은 게시글ID/내용 이렇게 필드를 구성할 수 있다. Post모델의 pk값을 게시글ID 필드에 넣어주는 것이다.**

```python
class Artist(models.Model):
    name = models.CharField(max_length=10)

class Album(models.Model):
    artist = models.ForeignKey(Artist, on_delete=models.CASCADE)
```

- 공식문서의 예제이다. ForeignKey 다음에 1:N관계를 맺고자 하는 모델의 이름을 적어준다.
- **on_delete라는 속성이 있다. 꼭 정의해줘야 하는 속성이다. RESTRICT 등 다양한 값이 많지만 가장 많이 사용되는 것은 on_delete=models.CASCADE 또는 PROTECT 이렇게 가장 많이 사용된다.**
  - **하위 클래스인 참조된 객체가 삭제될 때(ForeignKey가 삭제될 때), 삭제를 방지하는 게 PROTECT이다.**
  - **하위 클래스인 참조된 객체가 삭제될 때(ForeignKey가 삭제될 때), 같이 삭제되는 게 CASCADE이다. 즉, 게시글이 하나 삭제되면 관련된 댓글들이 전부 삭제가 되는 것이다.**


### M:N관계
- 이 경우에는 좋아요 기능이 여기에 해당될 수 있다. 1명의 사용자가 여러 게시글에 좋아요를 누를 수 있고 / 1개의 게시글은 여러 사용자들의 좋아요를 받을 수 있다.
