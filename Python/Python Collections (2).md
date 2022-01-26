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
{
#      '박효신': ['The Breeze OF Sea', 'Gift'], 
#      '나얼': ['Principle Of My Soul', 'Back to the Soul Flight'], 
#      '이수': ['pathos', 'Unveiling']
}

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
- 딕셔너리에서 특정 key-value를 삭제하려면 delete의 약자인 del 함수를 사용해 해당 key를 가진 key-value pair를 지워준다. 


```python
bumsoo = {
    '김범수': ['보고싶다', 'SOLISTA  Part 2']
}

albums.update(bumsoo)
print(albums)

# {'박효신': ['The Breeze OF Sea', 'Gift'], '나얼': ['Principle Of My Soul', 'Back to the Soul Flight'], '김범수': ['보고싶다', 'SOLISTA  Part 2']}
```

- 2개의 딕셔너리가 있을 때, 특정 딕셔너리에다가 다른 딕셔너리의 값을 전부 추가해서 갱신할 수 있다. update() 함수를 사용하자.


```python
bumsoo2 = {
    '김범수': ['보고싶다', 'SOLISTA Part 3']
}

bumsoo.update(bumsoo2)
print(bumsoo)           # {'김범수': ['보고싶다', 'SOLISTA Part 3']}
```

- 그리고 위와 같이, 동일한 key가 있는 딕셔너리 2개를 update하는 경우, 기존에 있는 value가 아닌 새롭게 update 되는 value가 적용된다.


## None이 출력되는 다른 case

```python
result = print(1000)
print(result)
```

- print함수는 계산하는 식이 아니라 단순히 출력하는 함수이다. 따라서 위와 같을 때, result라는 변수를 출력하면 아무것도 계산한 게 없어 None이 출력된다.


## 딕셔너리는 주로 데이터를 구조화하여 표현하고 싶은 경우에 활용

```python
# Youtube 채널 정보를 표현하기

channel = {
    'name': 'League of Legends',
    'subscribers': 1450000
}

# 이 channel 딕셔너리에서 직관적으로 데이터 활용 가능
print(f'채널명은 {channel["name"]}입니다.')         # f-string 사용 시, 작은따옴표에 안에 작은따옴표 사용 불가. 큰 따옴표로 데이터 접근
print(f"해당 채널의 구독자 수는 {channel['subscribers']}명 입니다.")
```

- 이렇게 표현하면, 내가 channel이라는 딕셔너리에 어떤 데이터를 원하는지 key값만 봐도 명시적으로 파악할 수 있다.


```python
# 딕셔너리를 여러 개 담고 있는 리스트를 구성하기
channels = [
    {
        'name': 'League of Legends',
        'subscribers': 1450000
    },
    {
        'name': '백종원 TV',
        'subscribers': 2300000
    },
    {
        'name': 'BTS Official',
        'subscribers': 4500000
    }
]

# 각 채널에 접근할 때는 인덱스로 접근
print(channels[2])          # {'name': 'BTS Official', 'subscribers': 4500000}

# 채널 내부의 정보에 접근할 떄는 key로 접근
print(channels[2]['name'])  # BTS Official
```

- 또한, 리스트와 딕셔너리를 혼합해서 다음과 같이 사용할 수 있다. 이 리스트 내부에서도 여러 개의 딕셔너리들을 이름 순서나 구독자 수 순서대로 정렬하는 방법도 있다. 


### 딕셔너리 내부 리스트 정렬해보기
```python
from operator import itemgetter
...

# itemgetter를 이용한 리스트 내부 딕셔너리 정렬
data = sorted(channels, key=itemgetter('subscribers'))      # 오름차순 정렬
data = sorted(channels, key=itemgetter('subscribers'), reverse=True)    # 내림차순 정렬

for d in data:
    print(d)
 
# {'name': 'League of Legends', 'subscribers': 6450000}
# {'name': 'BTS Official', 'subscribers': 4500000}
# {'name': '백종원 TV', 'subscribers': 2300000}
```

- operator 모듈의 itemgetter를 import하면 리스트나 튜플을 특정 기준에 따라 정렬할 수 있게 해준다.
- key=itemgetter('정렬 기준') 으로 sorted 함수는 오름차순 정렬이 되고, sorted 함수의 세번째 인자로 reverse=True를 하게 되면 내림차순 정렬이 된다.

