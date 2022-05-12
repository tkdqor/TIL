## 데이터를 다루는 Serializers (1)
- 공식문서 - API Guide - Serializers를 클릭해보자. https://www.django-rest-framework.org/api-guide/serializers/
- 가장 중요한 키워드는 "직렬화", "유효성 검사"이다. 
- Serializers는 django에서 Form 같은 존재이다. 
  - 잠깐 Form은 클라이언트가 보내온 데이터에 대한 유효성 검사를 해주고 처리를 해주었다. 그리고 HTML을 그려줬다. template에서 직접 form 태그를 작성하지 않아도 {{ form.as_p }} 이런식으로 html 
    태그들을 알아서 만들어주었다. 
    
- **Serializers는 데이터를 다른 형식으로 바꿔준다. 그러면서 유효성 검사를 직접 해준다.** 
  - **우리가 웹 개발에서 요청으로 어떤 데이터를 달라고 한다면, 우리의 모델에서 쿼리셋으로 데이터를 보내줄텐데 -> 이 때 Serializers를 사용하면 자동으로 모델 클래스를 JSON 또는 XML 형태로 바꿔서 보내준다. 이게 "직렬화"이다.**
  - 또한, 반대로 JSON 형태의 데이터가 요청되면 그걸 모델 형태로 바꿔주기도 한다. 이런 것을 "역직렬화" 라고 한다.


### 직렬화 예시
```python
from rest_framework import serializers

class CommentSerializer(serializers.Serializer):
    email = serializers.EmailField()
    content = serializers.CharField(max_length=200)
    created = serializers.DateTimeField()
```

- **이런식으로 serializers를 Import 해서 필드를 만들어줄 수 있다. 이렇게 정의를 하면,**

```python
serializer = CommentSerializer(comment)
serializer.data
# {'email': 'leila@example.com', 'content': 'foo bar', 'created': '2016-01-27T15:17:10.375877'}
```

- **해당 serializers를 사용해서 특정 모델을 넣어주면, serializer.data를 했을 때 --> JSON 형태의 데이터를 응답해줄 수 있는 것이다.**


### 역직렬화 예시
```python
import io
from rest_framework.parsers import JSONParser

stream = io.BytesIO(json)
data = JSONParser().parse(stream)
```

- **먼저 이렇게 JSON 형태의 데이터를 받을 수 있다.** 

```python
serializer = CommentSerializer(data=data)
serializer.is_valid()
# True
serializer.validated_data
# {'content': 'foo bar', 'email': 'leila@example.com', 'created': datetime.datetime(2012, 08, 22, 16, 20, 09, 822243)}
```

- **그 다음, serializer를 정의하고 is_valid() 메소드로 유효한 데이터인지를 검사해준다. 그래서 유효하다면 해당 데이터를 사용할 수 있게 된다.** 

```python
serializer = CommentSerializer(data={'email': 'foobar', 'content': 'baz'})
serializer.is_valid()
# False
serializer.errors
# {'email': ['Enter a valid e-mail address.'], 'created': ['This field is required.']}
```

- 또한, 이렇게 유효성 검사에 False로 실패한 경우에는, .errors 메소드를 사용해서 왜 틀렸는지 파악할 수 있다. 


