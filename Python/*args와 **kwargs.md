## *args란?
- *args는 *arguments의 줄임말.
- 함수에서 여러 개의 인자를 받고자 할 때 사용하게 된다.
```python
def lastName_and_FirstName(*args):
  for arg in args:
    print(arg[0], arg[1:3])
```

## *kwargs란?
- *kwargs는 *keyword argument의 줄임말.
- 함수에서 인자를 "키워드 = 특정 값" 의 형태로 받을 때 사용하게 된다.
```python
def EnglishName(*kwargs):
  for key, value in kwargs.items():
    print("{0} is {1}".format(key, value))
```


## 참고 자료
- https://brunch.co.kr/@princox/180
- https://wikidocs.net/24
