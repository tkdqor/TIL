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

* * * 

### ModelSerializer
- 우리가 일반적으로 데이터를 생성하게 되면 CRUD 행위들을 굉장히 많이 하게 된다. ModelSerializer는 일반적인 Serializer와 비슷하지만 더 쉽게 직렬화를 할 수 있게 해준다.
  - **일반 Serializer의 경우, 필드를 다 정의를 했었어야 했는데 / ModelSerializer 같은 경우에는,**

```python
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        fields = ['id', 'account_name', 'users', 'created']
```

- **이렇게 모델 이름을 입력해주면 모델의 필드를 가져올 수 있다. 모델에 정의되어있는 필드들을 그대로 가져와서 진행을 해준다.**
- 기본적으로 ModelSerializer는 모든 필드를 다 가지고 있지만, exclude를 사용해서 특정 필드를 제외할 수도 있다. 


- **중첩 직렬화 지정이란**
  - python은 객체 지향 언어이기 때문에 클래스안에 클래스, 객체안에 객체가 들어간다. 그래서 이 객체를 포함하는 형식의 코드가 있다. 

```python
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        fields = ['id', 'account_name', 'users', 'created']
        depth = 1
 ```
 
 - 이러한 예시가 있을 때, fields에 있는 'users'는 다른 객체일 것이다. Account라는 모델이 있을 것이고 User는 사용자 정보를 위한 모델일 것이다. 
   - 이 때, Serializers에서 depth를 정해주게 되면 -> Account의 필드들 이외에도 users라는 객체와 관련된 필드들도 들어가게 된다. 


- **기존 모델의 필드 이외에 필드를 추가**
```python
class AccountSerializer(serializers.ModelSerializer):
    url = serializers.CharField(source='get_absolute_url', read_only=True)
    groups = serializers.PrimaryKeyRelatedField(many=True)

    class Meta:
        model = Account
        fields = ['url', 'groups']
```

- 해당 예시처럼, 
