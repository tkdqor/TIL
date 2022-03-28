## render함수
```python
render(request, template_name, context=None, content_type=None, status=None, using=None)
```
- render함수의 경우, 특정 템플릿을 불러오는 함수로 세번째 인자인 context로 템플릿에 전달할 데이터를 딕셔너리 형태로 전달해줄 수 있다.


## redirect함수
```python
redirect(to, permanent=False, *args, **kwargs)
```
- redirect함수의 경우, 지정한 url로 이동시켜주는 함수로 그 url과 연결된 views 함수가 실행되고 이에 따라 다른 return값이 존재하게 된다.
- POST나 form을 통과시킨 이후에 새로운 페이지를 보여주기 위해 사용할 수 있다.
- 템플릿을 렌더링 해 줄 곳이 없어 이동해야 할 곳을 다른 곳으로 변경시켜주는 함수라고 볼 수 있다.
  - **이미 특정 url pattern으로 어떠한 template를 랜더링하게끔 설정이 되어있다면 -> 다른 View에서 해당 페이지로 render를 사용할 수 없다. 그럴 때는 redirect로 다른 페이지를 보여줘야 한다.**


## 참고자료
- https://free-eunb.tistory.com/38
- https://hwan-hobby.tistory.com/81
