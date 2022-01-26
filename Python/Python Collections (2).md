## 딕셔너리
- 딕셔너리는 중괄호로 여러 데이터를 묶어준다. 중괄호 안에 Key:Value의 Pair / 한글로 키:값 쌍이라고 하는데 이러한 형태의 값을 여러 개 작성할 수 있고 실제 데이터에 접근할 때에는 대괄호를 사용해서 인덱스 대신에 이 key값으로 데이터에 접근하게 된다.
- Value로 단순히 숫자나 문자열만 오는 게 아니라, 리스트나 또 다른 딕셔너리를 Value로 정의할 수도 있다.

```python
simple_dict = {'name': 'sangbaek', 'age': 29}

albums = {
    '박효신': ['The Breeze OF Sea', 'Gift'],
    '나얼': ['Principle Of My Soul', 'Back to the Soul Flight']
}

print(albums['나얼'])       # ['Principle Of My Soul', 'Back to the Soul Flight']

albums['이수'] = ['pathos', 'Unveiling']
print(albums)
# {
#      '박효신': ['The Breeze OF Sea', 'Gift'], 
#      '나얼': ['Principle Of My Soul', 'Back to the Soul Flight'], 
#      '이수': ['pathos', 'Unveiling']
# }

print(albums['김범수'])     # KeyError: '김범수'
```

- 딕셔너리에서 새로운 데이터를 추가하거나 기존 데이터를 수정할 때에도 -> 딕셔너리[새로운 Key] = 새로운 Value 이렇게 정의해주면 새롭게 데이터를 추가해줄 수 있다. 
- 딕셔너리 내부에 존재하지 않는 Key로 접근하게 될 경우에는, KeyError라는 것이 발생하면서 python 코드가 멈춰버리게 된다.  


```python
print(albums.get('김범수'))         # None

print(albums.get('이수'))           # ['pathos', 'Unveiling']

print(len(albums))                 # 3

del(albums['이수'])
print(albums)                      
#  {'박효신': ['The Breeze OF Sea', 'Gift'], '나얼': ['Principle Of My Soul', 'Back to the Soul Flight']}
```

- 딕셔너리에서 get 함수는 에러를 발생시키지 않고 해당 key로 값을 가져와보겠다는 것이다. 만약 기존 딕셔너리 내부에 존재하지 않는 key를 기반으로 get 함수를 사용하는 경우, None이 계산된다.
- 딕셔너리에서도 len 함수를 사용할 수 있는데, 해당 딕셔너리에 key-value pair가 몇 쌍이 있는지 알려준다.
- 딕셔너리에서 특정 key-value를 삭제하려면 delete의 약자인 del 함수를 사용해 해당 key를 가진 value값을 지워준다. 


```python
bumsoo = {
    '김범수': ['보고싶다', 'SOLISTA  Part 2']
}

albums.update(bumsoo)
print(albums)

# {'박효신': ['The Breeze OF Sea', 'Gift'], '나얼': ['Principle Of My Soul', 'Back to the Soul Flight'], '김범수': ['보고싶다', 'SOLISTA  Part 2']}
```

- 2개의 딕셔너리가 있을 때, 특정 딕셔너리에다가 다른 딕셔너리의 값을 전부 추가해서 갱신할 수 있다. update() 함수를 사용하자.
