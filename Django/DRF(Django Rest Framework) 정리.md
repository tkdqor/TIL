๐ **Contents**

- **DRF(Django Rest Framework) ์ ๋ฆฌ**
  - [DRF๋](#drf๋)
  - [์ํ ์ฝ๋](#์ํ-์ฝ๋)
  - [HTTP์ ํค๋](#http์-ํค๋)
  - [Django VS DRF ์ฐจ์ด์ ](#django-vs-drf-์ฐจ์ด์ )
  - [Serializer ์์ฑํ๊ธฐ](#serializer-์์ฑํ๊ธฐ)
  - [serializers.ModelSerializer](#serializers์-modelserializer)
  - [View ์์ฑํ๊ธฐ](#view-์์ฑํ๊ธฐ)
  - [APIView](#apiview)
  - [APIView ํด๋์ค์ ์ ์๋ check_object_permissions](#apiview-ํด๋์ค์-์ ์๋-check_object_permissions)
  - [is_valid ์ธ์์ raise_exception ์์ฑ ์ถ๊ฐ](#is_valid-์ธ์์-raise_exception-์์ฑ-์ถ๊ฐ)
  - [View์์ ํ๋ผ๋ฏธํฐ ๋ฐ๊ธฐ](#view์์-ํ๋ผ๋ฏธํฐ-๋ฐ๊ธฐ)
  - [URL ์ฐ๊ฒฐํ๊ธฐ](#url-์ฐ๊ฒฐํ๊ธฐ)
  - [DRF mixins](#drf-mixins)
  - [DRF generics](#drf-generics)
  - [DRF Viewset & Router](#drf-viewset๊ณผ-router)
  - [SerializerMethodField](#serializermethodfield)
  - [View์์ permission_classes๋ก ์ธ์ฆ๊ณผ ๊ถํ ์ค์ ํ๊ธฐ](#view์์-permission_classes๋ก-์ธ์ฆ๊ณผ-๊ถํ-์ค์ ํ๊ธฐ)
  - [์ฟผ๋ฆฌ์คํธ๋ง์ ๋ฐ์ ์ ์๋ URL ๋ง๋ค๊ธฐ](#์ฟผ๋ฆฌ์คํธ๋ง์-๋ฐ์-์-์๋-url-๋ง๋ค๊ธฐ)
  - [Serializer์์ filter ํจ์ ์ฌ์ฉํ๊ธฐ](#serializer์์-filter-ํจ์-์ฌ์ฉํ๊ธฐ)
  - [Serializer ํ๋ ์ค์ (read_only_fields ์ฌ์ฉํ๊ธฐ, Serializer ํ๋์ required=False ์ค์ )](#serializer-ํ๋-์ค์ )
  - [View์์ partial ์ค์ ](#view์์-partial-์ค์ )
  - [ํ์๊ฐ์๊ณผ ๋ก๊ทธ์ธ APIView ์์](#ํ์๊ฐ์๊ณผ-๋ก๊ทธ์ธ-apiview-์์)
  - [ํ์๊ฐ์๊ณผ ๋ก๊ทธ์ธ Serializer ์์](#ํ์๊ฐ์๊ณผ-๋ก๊ทธ์ธ-serializer-์์)
  - [DRF ๊ด๋ จ ์ฝ์ด๋ด์ผ ํ  ๋ธ๋ก๊ทธ](#drf-๊ด๋ จ-์ฝ์ด๋ด์ผ-ํ -๋ธ๋ก๊ทธ)



## DRF๋
- **Django REST Framework๋ Django๋ฅผ ๊ธฐ๋ฐ์ผ๋ก REST API ์๋ฒ๋ฅผ ๋ง๋ค๊ธฐ ์ํ ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ด๋ค.**
  - DRF๋ ์ด๋๊น์ง๋ Django๋ฅผ ๊ธฐ๋ฐ์ผ๋ก ํ ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ด๊ธฐ ๋๋ฌธ์ ์ ๋ฐ์ ์ผ๋ก Django์ ๊ฐ๋ฐ ํ๋ฆ์ ๋ฐ๋ผ๊ฐ๊ฒ ๋๋ค.

- API๋ ์น๋ฟ๋ง ์๋๋ผ ์ฑ๊ณผ ๊ฐ์ ๋ค์ํ ํ๋ซํผ์ ๋ฐฑ์๋ ์๋น์ค๋ฅผ ์ํด JSON๊ณผ ๊ฐ์ ๊ท๊ฒฉํ๋ ๋ฐ์ดํฐ๋ฅผ ์ ๊ณตํ๋ค. ๋ฐ๋ผ์ DRF๋ฅผ ์ฌ์ฉํ๋ฉด ๊ธฐ์กด์๋ ์์ฒด์ ์ธ ์น ํํ๋ฆฟ์๊ฒ ๋ฐ๋ก ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํด์ฃผ๋ Django ํ๋ก์ ํธ์ ๊ตญํ๋์๋ ๊ฒ์ -> JSON๊ณผ ๊ฐ์ ์์์ผ๋ก ๋ค์ํ ํ๋ซํผ์ ํด๋ผ์ด์ธํธ์๊ฒ ๋ฐ์ดํฐ๋ฅผ ์ ๊ณตํด์ค ์ ์๋ API ์๋ฒ๋ฅผ ๋ง๋ค ์ ์๊ฒ ๋๋ค.

- **๊ธฐ๋ณธ์ ์ผ๋ก View์์ method(GET,POST,PUT,DELETE ๋ฑ)์ ๋ฐ๋ฅธ ์๋ต ๋ก์ง์ ๊ตฌ์ฑํ๊ณ , ํ๋ผ๋ฏธํฐ ๊ฐ์ ๋ฐ์ status ์ฝ๋๋ฅผ ์ค์ ํ๊ณ , DB ๊ฐ์ฒด๋ฅผ ์กฐํํด์ -> ์๋ฆฌ์ผ๋ผ์ด์ ๋ก ๋ณด๋ด์ฃผ๋ฉด, ์๋ฆฌ์ผ๋ผ์ด์ ์์ ์ค์ ํ ๊ฐ์ฒด์ ํ๋๋ง JSON ๋ฐ์ดํฐ๋ก ์ง๋ ฌํ๋ฅผ ํด์ฃผ๋ ๊ณผ์ ์ด๋ค.**


<br>

### ์ํ ์ฝ๋
- ์ํ ์ฝ๋๋ ์์ฒญ์ ๋ํ ์๋ต์ด ์ด๋ค ์ํ์ธ์ง ๋ํ๋ด์ฃผ๋ ๊ท๊ฒฉํ๋ ์ฝ๋๊ฐ์ด๋ค. HTTP์์ ์ ์ํ๊ณ  ์๋ ๊ฐ์ผ๋ก, HTTP๋ฅผ ์ฌ์ฉํ๋ ๋ชจ๋  ์น ๊ฐ๋ฐ์๋ค์ด ํต์ฉ์ ์ผ๋ก ์ฌ์ฉํ๊ณ  ์๋ค.
- ๋ฐ๋ผ์, DRF์ Response์๋ ์ด ์ํ ์ฝ๋๊ฐ ๊ผญ ํฌํจ๋์ด ์๋ค. ๋ช ๊ฐ์ง ๋ํ์ ์ธ ์ํ ์ฝ๋๋ค์ ์๋์ ๊ฐ๋ค.

```python
HTTP_200_OK
# ๋ฐ์ดํฐ๋ฅผ ์์ฒญํ๋ GET ์์ฒญ์ด ์ ์์ ์ผ๋ก ์ด๋ค์ก์ ๋ ์๋ต์ ๋ํ๋๋ ์ํ๊ฐ

HTTP_201_CREATED
# ๋ฐ์ดํฐ๋ฅผ ์์ฑํ๋ POST ์์ฒญ์ด ์ ์์ ์ผ๋ก ์ด๋ค์ก์ ๋ ์๋ต์ ๋ํ๋๋ ์ํ๊ฐ

HTTP_206_PARTIAL_CONTENT
# ๋ฐ์ดํฐ๋ฅผ ์ผ๋ถ ์์ ํ๋ PATCH ์์ฒญ์ด ์ ์์ ์ผ๋ก ์ด๋ค์ก์ ๋ ์๋ต์ ๋ํ๋๋ ์ํ๊ฐ

HTTP_400_BAD_REQUEST
# ์๋ชป๋ ์์ฒญ์ ๋ณด๋์ ๋(ํด๋ผ์ด์ธํธ๊ฐ) ์๋ต์ ๋ํ๋๋ ์ํ๊ฐ

HTTP_401_UNAUTHORIZED
# ์ธ์ฆ์ด ํ์ํ๋ฐ ์ธ์ฆ ๊ด๋ จ ๋ด์ฉ์ด ์์ฒญ์ ์์ ๋ ์๋ต์ ๋ํ๋๋ ์ํ๊ฐ

HTTP_403_FORBIDDEN
# ํด๋ผ์ด์ธํธ๊ฐ ์ ๊ทผํ์ง ๋ชปํ๋๋ก ๋ง์๋์ ๊ณณ์ ์์ฒญ์ด ์์ ๋ ์๋ต์ ๋ํ๋๋ ์ํ๊ฐ

HTTP_404_NOT_FOUND
# ํด๋ผ์ด์ธํธ๊ฐ ์์ฒญ์ ๋ณด๋ธ ๊ณณ์ด ์๋ชป๋ URL์ผ ๋(์ฆ, ๋ฆฌ์์ค๊ฐ ์์ ๋) ์๋ต์ ๋ํ๋๋ ์ํ๊ฐ

HTTP_500_INTERNAL_SERVER_ERROR
# ์๋ฒ ์ชฝ์์ ์ฝ๋๊ฐ ์๋ชป๋์์ ๋ ์๋ต์ ๋ํ๋๋ ์ํ๊ฐ
```

<br>

### HTTP์ ํค๋
<img width="189" alt="image" src="https://user-images.githubusercontent.com/95380638/174439996-b0df3f13-a391-4d62-be5c-303222034988.png">

- **์์ ๊ฐ์ด Hello Api ํ๋ฉด์ ๋ณด์ฌ์ง๋ ๋ฐ์ดํฐ๋ค์ด ๋ฐ๋ก HTTP์ ํค๋ ๋ฐ์ดํฐ์ด๋ค.** 
- HTTP๋ ์น์ ํ๋ก ํธ์๋์ ๋ฐฑ์๋ ๊ฐ ์์ฒญ/์๋ต์ ์ํ ํ๋กํ ์ฝ์ด๋ค. HTTP๊ฐ ์์ฒญ/์๋ต์ ํ  ๋ ๋๊ตฌ๋ ๊ฐ์ ๋ฐฉ์์ผ๋ก ํต์ ์ ํ๊ธฐ ๋๋ฌธ์ ํต์ผ๋ ๊ท๊ฒฉ์ด ํ์ํ๊ณ , ๊ทธ ๊ท๊ฒฉ ๋ด์ ์๋ ์ฃผ์ ์ ๋ณด๋ฅผ ๋ด์ ๋์ ๊ฒ์ด ๋ฐ๋ก HTTP์ ํค๋์ด๋ค.
  - ์์ฒญ/์๋ต์ ๋ํ ๊ฒฐ๊ณผ ์ํ ๋ฐ ๋ฐ์ดํฐ์ ํ์, ์ํ ์ฝ๋ ๋ฑ์ด ์ ๋ถ HTTP ํค๋์ ํฌํจ๋๋ค.

<br>

### Django VS DRF ์ฐจ์ด์ 
- **Pure Django**
  - ๊ฐ๋ฐ ๋ชฉ์  : ์น ํ์คํ ๊ฐ๋ฐ
  - ๊ฐ๋ฐ ๊ฒฐ๊ณผ : ์น ํ์ด์ง๋ฅผ ํฌํจํ ์น ์๋น์ค
  - ์๋ต ํํ : HTML
  - ๋ค๋ฅธ ํ์ผ : templates

- **Django REST Framework**
  - ๊ฐ๋ฐ ๋ชฉ์  : ๋ฐฑ์๋ API ์๋ฒ ๊ฐ๋ฐ
  - ๊ฐ๋ฐ ๊ฒฐ๊ณผ : ์ฌ๋ฌ ํด๋ผ์ด์ธํธ์์ ์ฌ์ฉํ  ์ ์๋ API ์๋ฒ
  - ์๋ต ํํ : JSON
  - ๋ค๋ฅธ ํ์ผ : serializers.py

<br>

* * *

## Serializer ์์ฑํ๊ธฐ

<br>

### DRF Serializer
- **Serializer์ ์ฌ์ ์  ์๋ฏธ๋ ์ง๋ ฌํ์ด๋ค. ์ง๋ ฌํ๋ Django ํ๋ก์ ํธ์์ ๋ด๊ฐ ๋ง๋  ๋ชจ๋ธ๋ก๋ถํฐ ๋ฝ์ queryset, ์ฆ ๋ชจ๋ธ ์ธ์คํด์ค๋ฅผ JSON ํ์์ผ๋ก ๋ฐ๊พธ๋ ๊ฒ์ด๋ค.**
  - **์ฆ, serializer๊ฐ 2๊ฐ๋ผ๋ฉด View์์ 2๊ฐ์ ๊ฐ์ฒด๋ฅผ ๋ณด๋ด์ค์ผ ํ๋ค.**
- DRF ๋ด์์ ๋ฐ์ดํฐ๊ฐ ์ ์ฅ๋  ๋๋ Django์ ๋ชจ๋ธ์ ํตํด ์ ์ฅ๋๋ค. ๋ฐ๋ผ์ Django์์์ ๋ฐ์ดํฐ๋ JSON๊ณผ ๊ฐ์ ํฌ๋งท์ด ์๋, python ๊ฐ์ฒด์ ํํ๋ก ์ ์ฅ์ด ๋๋ค.
- ์ฐ๋ฆฌ๊ฐ ๋ง๋ค API ์๋ฒ๋ ์ด๋ฌํ ๋ฐ์ดํฐ๋ค์ ํด๋ผ์ด์ธํธ์๊ฒ ๋ณด๋ด์ฃผ๋ ์ญํ ์ ํด์ผํ๋ค.
  - ์ด ๋, ์ด๋ค ์์๋ ํ์ง ์๊ณ  ๋ฐ์ดํฐ๋ฅผ ๊ทธ๋๋ก ๋ณด๋ด์ค๋ค๋ฉด ํด๋ผ์ด์ธํธ๋ ์ฝ์ง๋ ๋ชปํ๋ python ๋ฐ์ดํฐ๋ฅผ ๋ฐ๊ฒ๋  ๊ฒ์ด๋ค.
  - **๋ฐ๋ผ์ ์ด๋ฅผ ์ํด python ๋ฐ์ดํฐ๋ฅผ ์ฝ์ ์ ์๋ ๋ฌธ์์ด(JSON ๋ฑ)๋ก ๋ณํํ์ฌ ๋ณด๋ด์ฃผ์ด์ผ ํ๊ณ , ์ด๋ ๊ฒ python ๋ฐ์ดํฐ ๊ฐ์ฒด๋ฅผ ๋ฌธ์์ด๋ก ๋ณํํ๋ ์์์ ์ง๋ ฌํ, Serializer๋ผ๊ณ  ์ดํดํ๋ฉด ๋๋ค.**

- **๋ฐ๋๋ก ํด๋ผ์ด์ธํธ๊ฐ ๋ฐ์ดํฐ๋ฅผ DRF ์๋ฒ์ ๋ณด๋ด์ฃผ๋ ๊ฒฝ์ฐ๋ ์๋ค.**
  - ex) POST ์์ฒญ์ ๋ณด๋ด๋ API๋ฅผ ํด๋ผ์ด์ธํธ๊ฐ ์ฌ์ฉํด ๋ฐ์ดํฐ๋ฅผ ์์ฑํ๋ ๊ฒฝ์ฐ, ํด๋ผ์ด์ธํธ๋ API ์์ฒญ์ ๋ฐ์ดํฐ๋ฅผ JSON ๋ฑ ๋ฌธ์์ด ํํ๋ก ์๋ ฅํ์ฌ ๋ณด๋ด์ค ๊ฒ์ด๋ค.
- DRF API ์๋ฒ ์์ฅ์์๋ ์ด ๋ฌธ์์ด์ ๊ทธ๋๋ก ๋ชจ๋ธ์ ํตํด ์ ์ฅํ  ์ ์๋ค. ๋ชจ๋ธ์ ํตํด ์ ์ฅํ๋ ค๋ฉด ๋ฐ์ดํฐ๊ฐ python ๊ฐ์ฒด์ ํํ์ฌ์ผ ํ๊ธฐ ๋๋ฌธ์ด๋ค. **๋ฐ๋ผ์ ์์  ๊ฒฝ์ฐ์ ๋ฐ๋๋ก JSON ๋ฑ ๋ฌธ์์ด์ python ๋ฐ์ดํฐ ๊ฐ์ฒด๋ก ๋ณํํ๋ ์์์ด ํ์ํ๋ฐ, ์ด๊ฒ์ ์ญ์ง๋ ฌํ, Deserializer๋ผ๊ณ  ํ๋ค.**

- ์ฐ๋ฆฌ๊ฐ ์์ผ๋ก ๋ง๋ค Serializer๋ ์ง๋ ฌํ์ ์ญ์ง๋ ฌํ ๊ธฐ๋ฅ์ ๋์์ ๊ฐ๊ณ  ์๋ค. ์ฆ, ํด๋ผ์ด์ธํธ์ ์๋ฒ API ๊ฐ ๋ฐ์ดํฐ ์์์ ๋ง์ถฐ์ฃผ๋ ๋ณํ๊ธฐ๋ผ๊ณ  ์๊ฐํ๋ฉด ๋๋ค.
  - [๊ด๋ จํด์ ์ข์ ๊ธ](https://velog.io/@jewon119/TIL136.-DRF-Serializer%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC)

- **๋ชจ๋ธ์ ๊ตฌ์ฑํ๊ณ  View๋ฅผ ์์ฑํ๊ธฐ ์ ์, ์ด ๋จ๊ณ์์ Serializers.py๋ฅผ ๋ง๋ค๊ณ  Serializer๋ฅผ ๊ตฌ์ํ๊ฒ ๋๋ค.**
  - ํ๋์ Serializer ํด๋์ค์๋ ๋ชจ๋ธ๊ณผ ๊ฐ์ด ํ๋๋ฅผ ์ค์ ํ  ์ ์๊ณ , ํด๋์ค ๋ด๋ถ์ ํจ์๋ ์ง์  ์ ์ํ  ์ ์๋ค.

```python
class BookSerializer(serializers.Serializer):
    bid = serializers.IntegerField()
    title = serializers.CharField(max_length=50)
    author = serializers.CharField(max_length=50)
    category = serializers.CharField(max_length=50)
    pages = serializers.IntegerField()
    price = serializers.IntegerField()
    published_date = serializers.DateField()
    description = serializers.TextField()

    def create(self, validated_data):
       return Book.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.bid = validated_data.get('bid', instance.bid)
        instance.title = validated_data.get('title', instance.title)
        instance.author = validated_data.get('author', instance.author)
        instance.category = validated_data.get('category', instance.category)
        instance.pages = validated_data.get('pages', instance.pages)
        instance.price = validated_data.get('price', instance.price)
        instance.published_date = validated_data.get('published_date', instance.published_date)
        instance.description = validated_data.get('description', instance.description)
        instance.save()
        
        return instance
```

- **์๋ฆฌ์ผ๋ผ์ด์ ๋ python ๋ชจ๋ธ ๋ฐ์ดํฐ๋ฅผ JSON์ผ๋ก ๋ฐ๊ฟ์ฃผ๋ ๋ณํ๊ธฐ์ด๊ธฐ ๋๋ฌธ์ ๋ชจ๋ธ ๋ฐ์ดํฐ์ ์ด๋ค ์์ฑ์ JSON์ ๋ฃ์ด์ค์ง ์ ์ธ์ ํด์ค์ผ ํ๋ค. ๊ทธ๋์ ์๋ฆฌ์ผ๋ผ์ด์ ์์ ํ๋๋ฅผ ์ ์ธํด์ฃผ๋ ๊ฒ์ด๋ค.**
- **๊ทธ๋ฆฌ๊ณ  create๋ update์ ๊ฐ์ ํจ์๋ ๋์ค์ POST ์์ฒญ์ผ๋ก ๋ค์ด์จ ๋ฐ์ดํฐ๋ฅผ ๋ค์ python ๋ชจ๋ธ ํํ๋ก ์ญ์ง๋ ฌํํ์ฌ ๋ฐ์ดํฐ๋ฒ ์ด์ค์ ์ ์ด๋ฃ์ ๋ ์ฌ์ฉ๋๋ ํจ์์ด๋ค. ์ด๋ ๊ฒ ์ ์ธํ๊ณ  ๋์ค์ serializer.save() ์ด๋ ๊ฒ ๋ฐ์ดํฐ๋ฅผ ์ ์ฅํ  ์ ์๋ค.**
  - instance.save() ์ฒ๋ผ .save()๋ ํด๋น ์ธ์คํด์ค๊ฐ ๋ฐ์ดํฐ๋ฒ ์ด์ค์ ์กด์ฌํ๋ ๊ฒฝ์ฐ, update ๋ฉ์๋๋ฅผ ์ด์ฉํ์ฌ ์ธ์คํด์ค๋ฅผ ์๋ฐ์ดํธ ํ๋ค.

<br>

### serializers์ ModelSerializer
```python
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['bid', 'title', 'author', 'category', 'pages', 'price', 'published_date', 'description',]
```

- ์์์ ๋ณด๋ฏ์ด serializers.Serializer๋ฅผ ์ฌ์ฉํ๊ฒ ๋๋ฉด ์ผ์ผ์ด ํ๋๋ค์ ์ ํด์ฃผ๊ณ  ์์ฑํด์ผํ  ์ฝ๋๋ค์ด ๋ง์์ง๋ค. ๊ทธ๋์ ๊ฐ๋ฐ์๋ค์ ๋ ๋์ ๋์์ธ serializers.ModelSerializer๋ฅผ ์ฌ์ฉํ๋ค.
- **ModelSerializer๋ ๋ชจ๋ธ์ ๋ด์ฉ์ ๊ธฐ๋ฐ์ผ๋ก ๋์ํ๋ ์๋ฆฌ์ผ๋ผ์ด์ ์ด๋ค. ์ด๋ฅผ ํตํด ์ฝ๋์ ์ค๋ณต์ ์ค์ผ ์ ์๊ณ  ํ๋ ์ ์ธ์ ๋ชจ๋ธ์์ ์ด๋ฏธ ํ๊ธฐ ๋๋ฌธ์ ModelSerializer์์๋ ๊ฐ๋จํ๊ฒ ์์ํ  ์ ์๋ค.**

```python
class TodoSimpleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = ('id', 'title', 'complete', 'important')


class TodoDetailSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = ('id', 'title', 'description', 'created', 'complete',
                  'important')


class TodoCreateSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = ('title', 'description', 'important')
```
- **์ด๋ฐ์์ผ๋ก ์๋ฆฌ์ผ๋ผ์ด์ ๋ง๋ค ๋ค๋ฅด๊ฒ ํ๋๋ฅผ ์ค์ ํ  ์ ์๋ค.**
  - ex) TodoCreateSerializer๋ Todo๋ฅผ ์์ฑํ  ๋ ํ์ํ ์๋ ฅ ๊ฐ์ด title๊ณผ description, important๋ผ์ 3๊ฐ์ ํ๋๋ง ์ ์ํ ๊ฒ์ด๋ค.


<br>

* * *

## View ์์ฑํ๊ธฐ
- **๊ธฐ๋ณธ์ ์ผ๋ก View ํด๋์ค๋ฅผ ๊ธฐ์ค์ผ๋ก URL์ ๋ถ๋ฆฌํ๋ค๊ณ  ์๊ฐํ๋ฉด ํธํ๋ค.**

<br>

### @api_view()
```python
@api_view(['GET'])
def HelloAPI(request):
    return Response("hello world!")
```

- **์์ ๊ฐ์ด @api_view(['GET'])๋ผ๋ ๋ฐ์ฝ๋ ์ดํฐ๋, helloAPI๋ผ๋ ํจ์๊ฐ API๋ก ๋์ํ๊ธฐ ์ํด ์๋ค๋ก ํจ์๋ฅผ ๊ฐ์ธ ์ค์  API๋ก ๋์ํ  ์ ์๋๋ก ํด์ค๋ค.** ๋ฐ๋ผ์ HelloAPI๋ผ๋ ํจ์๊ฐ GET ์์ฒญ์ ๋ฐ์ ์ ์๋ API๋ผ๋ ๊ฒ์ ํ์ํด์ค๋ค๊ณ  ๋ณด๋ฉด ๋๋ค.

- **์ฌ๊ธฐ์ request ๊ฐ์ฒด๋ ์ฌ์ฉ์๊ฐ ์ฐ๋ฆฌ DRF API ์๋ฒ๋ก ์์ฒญ์ ๋ณด๋ผ ๋, ์์ฒญ์ด GET, POST ๋ฑ ์ด๋ค ํ์์ธ์ง, ์ฌ์ฉ์๊ฐ ์์ฒญ์ ๋ณด๋ผ ๋ ๋ฌด์จ ๋ฐ์ดํฐ๋ฅผ ํจ๊ป ๋ณด๋ด์ฃผ์๋์ง ๋ฑ ์ ๋ณด๋ฅผ ๋ด๊ณ  ์๋ค.**
  - ex) request.method / request.data๋ก ์ ๊ทผ ๊ฐ๋ฅ

- **๊ทธ๋ฆฌ๊ณ  ๊ฒฐ๊ณผ๋ฅผ ๋ฐํํ  ๋๋ Response๋ผ๋ ํด๋์ค๋ฅผ ์ฌ์ฉํ๋ค.**
  - request์ ๋ง์ฐฌ๊ฐ์ง๋ก Response์๋ ์๋ต์ ๋ํ ์ ๋ณด๊ฐ ๋ด๊ฒจ์ ธ ์๋ค. response.data์๋ ์๋ต์ ํฌํจ๋๋ ๋ฐ์ดํฐ / response.status์๋ ์๋ต์ ๋ํ ์ํ๊ฐ ๋ํ๋๋ค.

<br>

### APIView
- django์์๋  DRF์์๋  View๋ฅผ ๊ฐ๋ฐํ๋ ๊ฒ์ ํฌ๊ฒ ํจ์ ๊ธฐ๋ฐ View์ธ FBV์ / ํด๋์ค ๊ธฐ๋ฐ View์ธ CBV๋ก ๋๋ ์ ์๋ค.
- **DRF์์๋ 2๊ฐ์ง๋ฅผ ๋ชจ๋ ๋์์ฃผ๋ APIView๋ผ๋ ๊ฒ์ด ์๋ค.**

- **ํจ์ ๊ธฐ๋ฐ View์์๋ @api_view์ ๊ฐ์ด ๋ฐ์ฝ๋ ์ดํฐ ํํ๋ก APIView๋ฅผ ์ฌ์ฉํ  ์ ์๋ค.**
```python
@api_view(['GET', 'POST'])  # GET/POST ์์ฒญ์ ์ฒ๋ฆฌํ๊ฒ ๋ง๋ค์ด์ฃผ๋ ๋ฐ์ฝ๋ ์ดํฐ
def booksAPI(request):
    if request.method == 'GET':      # GET ์์ฒญ์ผ ๋
        books = Book.objects.all()   # Book ๋ชจ๋ธ๋ก๋ถํฐ ์ ์ฒด ๋ฐ์ดํฐ ๊ฐ์ ธ์ค๊ธฐ
        serializer = BookSerializer(books, many=True)                 # ์๋ฆฌ์ผ๋ผ์ด์ ์ ์ ์ฒด ๊ฐ์ฒด๋ค์ ํ ๋ฒ์ ์ง์ด๋ฃ๊ธฐ(์ง๋ ฌํ, many=True) -> ์ด ๋, python ๊ฐ์ฒด๋ฅผ JSON ํํ๋ก ์ง๋ ฌํ ํ๋ค๋ ๊ฒ
        return Response(serializer.data, status=status.HTTP_200_OK)
    elif request.method == 'POST':   # POST ์์ฒญ์ผ ๋
        serializer = BookSerializer(data=request.data)                # POST ์์ฒญ์ผ๋ก ๋ค์ด์จ ๋ฐ์ดํฐ๋ฅผ ์๋ฆฌ์ผ๋ผ์ด์ ์ ์ง์ด๋ฃ๊ธฐ
        if serializer.is_valid():                                     # ์ ํจํ ๋ฐ์ดํฐ๋ผ๋ฉด
            serializer.save()                                         # ์๋ฆฌ์ผ๋ผ์ด์ ์ ์ญ์ง๋ ฌํ๋ฅผ ํตํด save(), ์ด ๋ ModelSerializer์ ๊ธฐ๋ณธ create() ํจ์๊ฐ ๋์
            return Response(serializer.data, status=status.HTTP_201_CREATED)   # 201 ๋ฉ์์ง๋ฅผ ๋ณด๋ด๋ฉฐ ์ฑ๊ณต
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST) # 400์ ์๋ชป๋ ์์ฒญ์ผ ๊ฒฝ์ฐ ์๋ต

@api_view(['GET'])
def bookAPI(request, bid):
    book = get_object_or_404(Book, bid=bid)                           # bid = id์ธ ๋ฐ์ดํฐ๋ฅผ Book์์ ๊ฐ์ ธ์ค๊ณ  ์์ผ๋ฉด 404 ์๋ฌ
    serializer = BookSerializer(book)                                 # ์๋ฆฌ์ผ๋ผ์ด์ ์ ๊ฐ์ฒด๋ฅผ ์ง์ด๋ฃ๊ธฐ(์ง๋ ฌํ)
    return Response(serializer.data, status=status.HTTP_200_OK) 
```
- **/book/ ์ฃผ์๋ฅผ ์ฌ์ฉํ  ๋ API์ ๋ํ ์ฒ๋ฆฌ๋ booksAPI()๋ผ๋ ํจ์์์ ํ ๋ฒ์ ์ฒ๋ฆฌํ๋ค.**
  - ๋ฐ์ฝ๋ ์ดํฐ๋ก GET, POST๋ฅผ ํจ๊ป ์ฒ๋ฆฌํ  ์ ์๋๋ก ์ค์ ํ๊ณ  ์กฐ๊ฑด๋ฌธ์ ํตํด ํด๋น ์์ฒญ์ด GET์ธ์ง POST์ธ์ง์ ๋ฐ๋ผ ๋ค๋ฅด๊ฒ ์ฒ๋ฆฌํ๋ค.
  - **GET ์์ฒญ์ ๋์ ์ ์ฒด ์ ๋ณด๋ฅผ ๊ฐ์ ธ์ค๋๊น ๋ชจ๋ธ๋ก๋ถํฐ ๊ฐ์ฒด๋ค์ ๊ฐ์ ธ์ ์๋ฆฌ์ผ๋ผ์ด์ ์ ์ง์ด๋ฃ์ด์ -> ์ง๋ ฌํ๋ฅผ ํ๊ณ  ๊ทธ๋ ๊ฒ ๊ฐ๊ณต๋ ๋ฐ์ดํฐ๋ฅผ ๊ฒฐ๊ณผ๋ก ์๋ตํ๋ค. ex) serializer = BookSerializer(books, many=True)**
  - **์๋ฆฌ์ผ๋ผ์ด์ ์ ๋ฃ์ ๋, many=True ์ต์์ ๋ฃ์ผ๋ฉด, ์ฌ๋ฌ ๊ฐ์ฒด๋ค์ ๋ํ ์ฒ๋ฆฌ๋ฅผ ํ  ์ ์๋๋ก ํด์ค๋ค.**
  - **POST ์์ฒญ์ ์คํ๋ ค ์์ฒญ์ผ๋ก ๋ค์ด์จ ๋ฐ์ดํฐ๋ฅผ ์ญ์ง๋ ฌํํ์ฌ ๋ชจ๋ธ์ ์ง์ด๋ฃ์ด์ผ ํ๋ฏ๋ก -> ๋จผ์  ์๋ฆฌ์ผ๋ผ์ด์ ์ serializer = BookSerializer(data=request.data) ์ด๋ ๊ฒ request.data๋ฅผ ๋ฃ์ด์ค๋ค.**
  - ๊ทธ๋ฆฌ๊ณ  ์๋ฆฌ์ผ๋ผ์ด์ ์ is_valid() ๊ธฐ๋ฅ์ ํตํด ๋ค์ด์จ ๋ฐ์ดํฐ๊ฐ ๋ชจ๋ธ์ ๋ง๋ ์ ํจํ ๋ฐ์ดํฐ๋ผ๋ฉด ์ด๋ฅผ ์ ์ฅํ๋ค.
  - ๋ง์ง๋ง์ผ๋ก serializer.save()๋ ๊ธฐ๋ณธ์ ์ธ create() ํจ์๋ฅผ ์คํ์ํค๋ ModelSerializer์ ๊ธฐ๋ฅ์ด๋ค. ๋ฐ์ดํฐ๊ฐ ์ ์ ์ฅ๋์๋ค๋ฉด 201 ๋ฉ์์ง๋ฅผ ๋ณด๋ด๊ณ  ์๋๋ฉด 400 ๋ฉ์์ง๋ฅผ ๋ณด๋ด์ ๋ง๋ฌด๋ฆฌํ๋ค.

- **bookAPI ํจ์๋ ๋ง์ฐฌ๊ฐ์ง์ด๋ค.**
  - ํน์  bid์ ์ฑ ๋ฐ์ดํฐ๋ฅผ ๊ฐ์ ธ์์ ํจ์์ ์ธ์๋ก bid๋ฅผ ๋๊ฒจ๋ฐ์ ๋ชจ๋ธ์์ ์ฐพ๋๋ค. ๊ทธ๋ฆฌ๊ณ  ์ฐพ์ ๊ฐ์ฒด๋ฅผ serializer = BookSerializer(book) ์ด๋ ๊ฒ ์ง๋ ฌํํด์ค๋ค.

<br>

- **๊ทธ๋ฆฌ๊ณ  ํด๋์ค ๊ธฐ๋ฐ View์์๋ APIView๋ผ๋ ํด๋์ค๋ฅผ ์์๋ฐ๋ ํด๋์ค์ ํํ๋ก ์์ฑํ  ์ ์๋ค.**
```python
class BooksAPI(APIView):
    def get(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK) 
    def post(self, request):
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED) 
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class BookAPI(APIView):
    def get(self, request, bid):
        book = get_object_or_404(Book, bid=bid)
        serializer = BookSerializer(book)
        return Response(serializer.data, status=status.HTTP_200_OK)
```

- ํจ์ํ View์ ๊ธฐ๋ฅ์ ์ผ๋ก๋ ํฐ ์ฐจ์ด๊ฐ ์๋ค. 
- **์ฐจ์ด์ ์ ํด๋์ค ๋ด์ get๊ณผ post๋ฅผ ๋ฐ๋ก ์ ์ํด์ฃผ๊ธฐ ๋๋ฌธ์ ๋ฐ์ฝ๋ ์ดํฐ๊ฐ ํ์์๊ณ  ํด๋น ์์ฒญ์ด GET์ธ์ง POST์ธ์ง ์กฐ๊ฑด๋ฌธ์ผ๋ก ๋ฐ์ ธ๋ณผ ํ์๊ฐ ์๋ค.**

- get ๋ฉ์๋ ๋ด๋ถ์ ์๋ return Response(serializer.data, status=status.HTTP_200_OK) ํด๋น ์ฝ๋์์ **serializer.data๋ฅผ ๋ณ์์ ๋ด๊ณ  ์ถ๋ ฅํด๋ณด๋ฉด, [ํด๋น ๋ธ๋ก๊ทธ](https://velog.io/@jewon119/TIL136.-DRF-Serializer%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC)์ ๋์์๋ฏ์ด ๋์๋๋ฆฌ ํํ์ ๊ฐ์ฒด๋ก ๋ฐ์ ์ ์๊ฒ ๋๋ค. ReturnDict์ด๋ผ๋ ํ์์ผ๋ก DRF์์ ์ง์ํ๋ ๋์๋๋ฆฌ ํํ์ด๋ค.**
  - ๊ทธ ๋ค์ ์ด์  JSON ํฌ๋งท์ Byte String ํ์์ผ๋ก ํด๋ผ์ด์ธํธ์ ์ ๋ฌํ๊ธฐ ์ํด JSONRenderer๋ฅผ ์ฌ์ฉํ  ์ ์๋ค.

<br>

- **Serializing๊ณผ Deserializing ๋น๊ต**
  - **Serializing**์ Database๋ก๋ถํฐ Object๋ฅผ ๊ฐ์ ธ์ Serializer์ instance ์ธ์์ ์ ๋ฌํ์ฌ dict ๋ฐ์ดํฐ๋ก ๋ณํํ๋ค. ์ด dict ๋ฐ์ดํฐ๋ฅผ JSON ํฌ๋งท์ Byte๋ก ๋ณํํ์ฌ ํด๋ผ์ด์ธํธ์ ์ ๋ฌํ๋ ํ๋ฆ์ด๋ฉฐ, ์ด ๊ณผ์ ์์ JSONRenderer๊ฐ ์ฌ์ฉ๋๋ค.
  - ๋ฐ๋๋ก, **Deserializing**๋ ํด๋ผ์ด์ธํธ๋ก ์ ๋ฌ๋ฐ์ JSON ํฌ๋งท์ Byte ๋ฐ์ดํฐ๋ฅผ JSONParser์ parse ๋งค์๋๋ฅผ ํตํด ์ฐ์  dict ๋ฐ์ดํฐ๋ก ๋ณํํ๋ค. ์ด dict ๋ฐ์ดํฐ๋ฅผ ๋ฐ๋ก ์ฌ์ฉํ๋ ๊ฒ์ด ์๋, Serializer์ data ์ธ์์ ์ ๋ฌํ์ฌ is_valid ๋งค์๋๋ก ์ ํจ์ฑ ๊ฒ์ฌ๋ฅผ ์งํํ๋ค. ์ ํจ์ฑ ๊ฒ์ฌ์ ๋ฌธ์ ๊ฐ ์๋ค๋ฉด errors์ ์๋ฌ ์ ๋ณด๊ฐ ๋ด๊ธฐ์ง๋ง, ์ ํจ์ฑ ๊ฒ์ฌ๋ฅผ ํต๊ณผํ๋ค๋ฉด validated_data ์์ฑ์ ๋ฐ์ดํฐ๊ฐ ์กด์ฌํ๋ค. validated_data๋ฅผ ๋ง๋ค๊ณ  ๋์์ผ ์ด ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํด์ ๋ชจ๋ธ์ ์ ๋ฌํด ์ธ์คํด์ค๋ฅผ ๋ง๋ค๊ณ , Database์ ์ ์ฅํ๋ค.

```python
class TodosAPIView(APIView):
    def get(self, request):
        todos = Todo.objects.filter(complete=False)
        serializer = TodoSimpleSerializer(todos, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        serializer = TodoCreateSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class TodoAPIView(APIView):
    def get(self, request, pk):
        todo = get_object_or_404(Todo, id=pk)
        serializer = TodoDetailSerializer(todo)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def put(self, request, pk):
        todo = get_object_or_404(Todo, id=pk)
        serializer = TodoCreateSerializer(todo, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class DoneTodosAPIView(APIView):
    def get(self, request):
        dones = Todo.objects.filter(complete=True)
        serializer = TodoSimpleSerializer(dones, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)


class DoneTodoAPIView(APIView):
    def get(self, request, pk):
        done = get_object_or_404(Todo, id=pk)
        done.complete = True
        done.save()
        serializer = TodoDetailSerializer(done)
        return Response(status=status.HTTP_200_OK)
```

- **์์๋ Todo๋ผ๋ ๋ชจ๋ธ CRUD API๋ฅผ APIView๋ก ์์ฑํ ์์์ด๋ค.**


<br>

### APIView ํด๋์ค์ ์ ์๋ check_object_permissions
- Custom Permission๋ฅผ ์ ์ฉํ  ๋, APIView ํด๋์ค์ ์ ์๋ check_object_permissions ๋ฉ์๋๋ฅผ overrideํด์ ๊ฒ์ฌ๋ฅผ ์งํํ  ์ ์๋ค.
- **Custom Permission๋ฅผ ์ฌ์ฉํ๊ณ  ์์ ๋, APIView์์ ์ธ์คํด์ค ๋ ๋ฒจ์ ๊ฒ์ฌ๊ฐ ์๋ํ๋ ค๋ฉด View์ ์ฝ๋๊ฐ ๋ช์์ ์ผ๋ก .check_object_permissions ๋ฉ์๋๋ฅผ ํธ์ถํด์ผ ํ๋ค.**
  - ํด๋น ๋ฉ์๋๋ APIView์ ๋ด์ฅ ๋ฉ์๋์ด๊ธฐ ๋๋ฌธ์, ํจ์ ๊ธฐ๋ฐ ๋ทฐ์์๋ ํธ์ถํ  ์๊ฐ ์๋ค.

```python
class AccountBooksRecordAPIView(APIView):
    # Custom Permission
    permission_classes = [IsOwner]   
    def get_object_and_check_permission(self, obj_id):
    try:
            object = AccountBook.objects.get(id=obj_id)
    except AccountBook.DoesNotExist:
            return

    self.check_object_permissions(self.request, object)
    return object
```

- IsOwner๋ผ๋ ์ปค์คํ๋ Permission๋ฅผ ์ฌ์ฉํ๊ณ  ์๊ธฐ ๋๋ฌธ์ self.check_object_permissions(self.request, object) ์ด๋ ๊ฒ APIView์ ๋ด์ฅ ๋ฉ์๋๋ฅผ ํธ์ถํ์ฌ ๊ฒ์ฌ๋ฅผ ์งํํด์ผ ํ๋ค.


<br>

### is_valid ์ธ์์ raise_exception ์์ฑ ์ถ๊ฐ
- View์์ ์๋ชป๋ ์์ฒญ์ด ๋ค์ด์์ ๋, ์ฌ์ฉ์์๊ฒ ์ ์ ํ ์๋ต์ ๋ณด๋ด๊ธฐ ์ํด์๋ .is_valid() ์ ์ธ์์ raise_exception=True ์์ฑ์ ์ถ๊ฐํ  ์ ์๋ค.

```python
...
class AccountBooksRecordDetailAPIView(APIView):
    ...
    def put(self, request, record_id):
        ...
        serializer = AccountBooksRecordModelSerializer(record, data=request.data, partial=True)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response({"message": "๊ธฐ๋ก ์์  ์ฑ๊ณต!!"}, status=status.HTTP_200_OK)
```
- **์์ ๊ฐ์ด serializer.is_valid(raise_exception=True) ๋ผ๊ณ  raise_exception=True ์์ฑ์ ์ถ๊ฐํ๋ฉด, ํด๋น ๋ฐ์ดํฐ๊ฐ ์ ํจ์ฑ ๊ฒ์ฌ๋ฅผ ํต๊ณผํ์ง ๋ชปํ  ๋ REST framework๊ฐ ์ ๊ณตํ๋ ๊ธฐ๋ณธ exception handler์์ 400 ์๋ฌ๋ฅผ ๋ฐํํด์ค๋ค.**

<br>

### View์์ ํ๋ผ๋ฏธํฐ ๋ฐ๊ธฐ
- **URL์ ?status=delete์ ๊ฐ์ด ํ๋ผ๋ฏธํฐ๋ฅผ ๋ฐ๊ธฐ ์ํด์๋, View์์ request.GET.get("status", None) ์ด๋ฌํ ์ฝ๋๋ก ๋ฐ์ ์ ์๋ค.**

```python
class AccountBooksAPIView(APIView):
    ...
    def get(self, request):
        data_status = request.GET.get("status", None)
        if data_status == "delete":
            account_books = AccountBook.objects.filter(user=request.user, is_deleted=True)

        else:
            account_books = AccountBook.objects.filter(user=request.user, is_deleted=False)
        serializer = AccountBooksModelSerializer(account_books, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)
```

<br>

### URL ์ฐ๊ฒฐํ๊ธฐ
- **ํจ์ํ View๋ฅผ ์ฐ๊ฒฐํ  ๋๋ ๋ค์๊ณผ ๊ฐ๋ค.**

```python
urlpatterns = [
    path("hello/", helloAPI),
    path("fbv/books/", booksAPI),
    path("fbv/book/<int:bid>/", bookAPI),
]
```

- **๊ทธ๋ฆฌ๊ณ  ํด๋์คํ View๋ฅผ ์ฐ๊ฒฐํ  ๋๋ ๋ค์๊ณผ ๊ฐ๋ค. ํจ์ํ View์ ๋ฌ๋ฆฌ path์ ๋ฑ๋กํ  ๋ .as_view()๋ฅผ ์ฌ์ฉํ๋ค.**

```python
urlpatterns = [
    path("hello/", helloAPI),
    path("fbv/books/", booksAPI),
    path("fbv/book/<int:bid>/", bookAPI),
    path("cbv/books/", BooksAPI.as_view()),
    path("cbv/book/<int:bid>/", BookAPI.as_view()),
]
```

- **ํจ์ํ View์ด๋ , ํด๋์คํ View์ด๋  ๊ฒฐ๊ตญ์ ๊ฐ request์ method(GET, POST ๋ฑ)์ ๋ฐ๋ผ ์ฒ๋ฆฌํ  ์ ์๋๋ก ์์ฑ๋๋ค.**
  - ex) 127.0.0.1/books/ => GET(์ ์ฒด ๋ชฉ๋ก) / POST(์์ฑ)
  - ex) 127.0.0.1/books/1 => GET(๊ฐ์ ธ์ค๊ธฐ) / PUT(์์ ) / DELETE(์ญ์ )

- **์ผ๋ฐ์ ์ธ DRF ๊ฐ๋ฐ ์ ์ฐจ๋ Model -> Serializers -> Views -> Urls ๋ผ๊ณ  ๋ณผ ์ ์๋ค.**

- **์ง๊ธ๊น์ง๋ DRF์ ๊ธฐ๋ณธ ํ์ด์ง๋ก API ํ์คํธ๋ฅผ ์งํํ์ง๋ง, Insomnia๋ผ๋ API ํ์คํธ ๋ฌด๋ฃ ํด๋ ์กด์ฌํ๋ค.**

<br>

### DRF์ ๋ค์ํ ๋ทฐ
- ์ผ๋ฐ์ ์ผ๋ก URL๊ณผ Method์ ์กฐํฉ์ ํฌ๊ฒ ์ด 5๊ฐ์ ์กฐํฉ์ด ๋์จ๋ค.

```python
# ๋์ ์ ์ฒด ๋ชฉ๋ก ๊ฐ์ ธ์ค๊ธฐ (GET /books/), (list)
# ๋์ 1๊ถ ์ ๋ณด ๋ฑ๋กํ๊ธฐ (POST /books/), (create)
# ๋์ 1๊ถ ์ ๋ณด ๊ฐ์ ธ์ค๊ธฐ (GET /book/1/), (retrieve)
# ๋์ 1๊ถ ์ ๋ณด ์์ ํ๊ธฐ (PUT /book/1/), (update)
# ๋์ 1๊ถ ์ ๋ณด ์ญ์ ํ๊ธฐ (DELETE /book/1/), (destroy)
```

- **์ฌ๊ธฐ์ ์ด๋ค ๋ชจ๋ธ์ ๋ํด retrieve๋ฅผ ํ๋ค๊ณ  ํ๋ฉด ์ฐ๋ฆฌ๋ ๊ทธ ๋ชจ๋ธ์ 1๊ฐ ๊ฐ์ฒด ์ ๋ณด๋ฅผ ๊ฐ์ ธ์จ๋ค๊ณ  ์๊ฐํ๋ฉด ๋๋ค.**
- **์ผ๋ฐ์ ์ผ๋ก DRF๋ก API๋ฅผ ๊ฐ๋ฐํ  ๋ ๋ง๋ค์ด์ผ ํ๋ ๊ธฐ๋ฅ์ ์์ 5๊ฐ์ง์ด๋ค. ๊ทธ๋์ ๊ฐ๋ฐ์๋ค์ ์ด๋ฅผ ์ต๋ํ ํธํ๊ณ  ์ฝ๊ฒ ๋ง๋ค๊ณ ์ mixins, generics, Viewset๊น์ง ๋ฐ์ ํด์๋ค.**


<br>

### DRF mixins
- **์ฐ๋ฆฌ๊ฐ ์์์ ์์ฑํ ํด๋์ค ๊ธฐ๋ฐ View๋ฅผ ๋ค์๋ณด๋ฉด ์ค๋ณต๋๋ ์ฝ๋๋ค์ด ์๋ค๋ ๊ฒ์ ์ ์ ์๋ค. ๊ทธ๋์ ์ด๋ฌํ ์ค๋ณต์ ์ ๊ฑฐํ๊ธฐ ์ํด DRF์์๋ mixins๋ผ๋ ๊ฒ์ ๋ฏธ๋ฆฌ ์ ์ํ๊ณ  ์๋ค.**
  - **mixins๋ APIView์์ request์ method๋ง๋ค ์๋ฆฌ์ผ๋ผ์ด์  ์ฝ๋๋ฅผ ์์ฑํ๋ ๊ฒ์ ์ค์ด๊ธฐ ์ํด ํด๋์ค ๋ ๋ฒจ์์ ์๋ฆฌ์ผ๋ผ์ด์ ๋ฅผ ๋ฑ๋กํ๊ณ  ์๋ค.**
  - ๊ทธ๋์ ๊ฐ method์๋ ์๋ฆฌ์ผ๋ผ์ด์  ์ฝ๋๋ฅผ ์์ฑํ๋ ๋์  ๊ฐ method๋ณ mixin์ ์ฐ๊ฒฐํ์ฌ ์ฌ์ฉํ๊ธฐ๋ง ํ๋ฉด ๋๋ค.

```python
from rest_framework import generics
from rest_framework import mixins

class BooksAPIMixins(mixins.ListModelMixin, mixins.CreateModelMixin,generics.GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):          # GET ๋ฉ์๋ ์ฒ๋ฆฌ ํจ์(์ ์ฒด ๋ชฉ๋ก)
        return self.list(request, *args, **kwargs)    # mixins.ListModelMixin๊ณผ ์ฐ๊ฒฐ
    def post(self, request, *args, **kwargs):         # POST ๋ฉ์๋ ์ฒ๋ฆฌ ํจ์ (1๊ถ ๋ฑ๋ก)
        return self.create(request, *args, **kwargs)  # mixins.CreateModelMixin๊ณผ ์ฐ๊ฒฐ

class BookAPIMixins(mixins.RetrieveModelMixin, mixins.UpdateModelMixin, mixins.DestroyModelMixin, generics.GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    lookup_field = 'bid'                                 # django์ ๊ธฐ๋ณธ ๋ชจ๋ธ pk๊ฐ ์๋ ์ฌ๊ธฐ์๋ bid๋ฅผ pk๋ก ์ฌ์ฉํ๊ณ  ์์ผ๋ lookup_field๋ก ์ค์ 

    def get(self, request, *args, **kwargs):             # GET ๋ฉ์๋ ์ฒ๋ฆฌ ํจ์(1๊ถ)
        return self.retrieve(request, *args, **kwargs)   # mixins.RetrieveModelMixin๊ณผ ์ฐ๊ฒฐ
    def put(self, request, *args, **kwargs):             # PUT ๋ฉ์๋ ์ฒ๋ฆฌ ํจ์(1๊ถ ์์ )
        return self.update(request, *args, **kwargs)     # mixins.UpdateModelMixin๊ณผ ์ฐ๊ฒฐ
    def delete(self, request, *args, **kwargs):          # DELETE ๋ฉ์๋ ์ฒ๋ฆฌ ํจ์(1๊ถ ์ญ์ )
        return self.destroy(request, *args, **kwargs)    # mixins.DestroyModelMixin๊ณผ ์ฐ๊ฒฐ
```

- **์ด๋ ๊ฒ ๋์ผํ ๋์ ์ ๋ณด API๋ฅผ mixins๋ก ๋ค์ ๋ง๋ค์ด๋ดค๋ค.**
- ์ฐ์  generics์ mixins๋ฅผ import ํด์ ํด๋์ค๋ฅผ ๋ง๋ค ๋ ์์ํด์ค๋ค.
  - BooksAPIMixins ํด๋์ค์ ๊ฒฝ์ฐ, ListModelMixin๊ณผ CreateModelMixin์ ์์ํ๊ณ  / BookAPIMixins ํด๋์ค์ ๊ฒฝ์ฐ, RetrieveModelMixin๊ณผ UpdateModelMixin ๊ทธ๋ฆฌ๊ณ  DestroyModelMixin๋ฅผ ์์ํ๊ณ  ์๋ค.
  - ๊ณตํต์ ์ผ๋ก๋ generics.GenericAPIView๋ฅผ ์์ํ๊ณ  ์๋ค.

- **๊ทธ๋ฆฌ๊ณ  ๊ณตํต์ ์ผ๋ก Mixins ํด๋์ค ๋ด๋ถ ๋ณ์์๋ queryset๊ณผ serializer_class๋ผ๋ ๋ณ์๊ฐ ์๋ค.**
  - queryset์๋ ๋ชจ๋ธ์ ์ง๋ฌธ์ ๋ณด๋ด ๋ฐ์์จ ๋ฐ์ดํฐ๊ฐ ๋ค์ด๊ฐ๋๋ฐ ์ผ๋ฐ์ ์ผ๋ก ๊ทธ๋ฅ ๋ชจ๋  ๋ฐ์ดํฐ๋ฅผ ๋ถ๋ฌ์จ๋ค. ex) queryset = Book.objects.all()
  - ๊ทธ๋ฆฌ๊ณ  serializer_class์๋ ํด๋น API์์ ์ฌ์ฉํ  ์๋ฆฌ์ผ๋ผ์ด์ ๋ฅผ ์ค์ ํ๋ค. ex) serializer_class = BookSerializer ์ด๋ ๊ฒ ์ค์ ํด์ค๋ค.

- **๋ํ, ๊ฐ ํด๋์ค ๋ด๋ถ์ ํจ์๋ช์ ๋ฉ์๋๋ช๊ณผ ๋์ผํ๋ค. ex) def get**
  - ์ด ๋ถ๋ถ์ ํด๋์คํ View์ ๋์ผํ์ง๋ง ํจ์์ ๋ด์ฉ์ด ์์ด์ง๊ณ  ๋ฐ๋ก return์ ํ๋ค. ์ด ๋, return ํ๋ ๋ถ๋ถ์ ๋ณด๋ฉด ๊ฐ๊ฐ ๋ค๋ฅธ ๊ฒ์ ๋ฐํํ๊ณ  ์๋๋ฐ, ์ด๋ ๊ฐ ๋ฉ์๋ ๋ณ ์ฒ๋ฆฌํ๋ ๊ธฐ๋ฅ์ ๋ฐ๋ผ, ์์๋ฐ์ ์จ mixin ์ค ์ด๋ค ๊ฒ์ ์ฐ๊ฒฐํ  ๊ฒ์ด๋์ ๋ฐ๋ผ ๋ฌ๋ผ์ง๊ฒ ๋๋ค.

- **์ด๋ ๊ฒ Mixin๋ฅผ ์ฌ์ฉํ๋ฉด, API ํ์คํธ ํ๋ฉด์์ DRF๊ฐ ํจ๊ป ์ ๊ณตํด์ฃผ๋ ํํ๋ฆฟ์ผ๋ก ์ผ์ผ์ด JSON์ ์๋ ฅํ์ง ์๊ณ  ํ๋๋ง๋ค ์๋ ฅํ  ์ ์๋ Form์ด ์์ฑ๋๋ค.**

<br>

### DRF generics
- mixins ๋ฐฉ๋ฒ์ REST API์ ๋ฉ์ธ ๊ธฐ๋ฅ์ ์ฝ๋ 10์ฌ์ค๋ก ๋๋ธ๋ค๋ ์ ์์ ํ๊ธฐ์ ์ด๊ณ  ๊ฐ๋จํ๋ค. ๊ทธ๋ฌ๋ ์ด๊ฒ๋ง์ ๋ ๊ท์ฐฎ๋ค๋ ์๊ฐ์ ํ ๊ฐ๋ฐ์๋ค์ด ์์๋ค. mixins ๋ฐฉ๋ฒ์์ ์ฌ์ฉํ  mixins๋ฅผ ์์๋ฐ๋๋ฐ ํ ๋ฒ์ 2~3๊ฐ์ฉ ์์์ ๋ฐ์์ผ ํ๊ธฐ ๋๋ฌธ์ด๋ค.
- **๊ทธ๋์ DRF์๋ mixins๋ฅผ ์กฐํฉํด์ ๋ฏธ๋ฆฌ ๋ง๋ค์ด๋ ์ผ์ข์ mixins ์ธํธ๊ฐ ์๋ค. ์ด 9๊ฐ์ ์กฐํฉ ์ข๋ฅ๊ฐ ์๋ค. ์ด ์ข๋ฅ๊ฐ generics ํ์์ ์กด์ฌํ๋ค.**

```python
# (1) generics.ListAPIView (์ ์ฒด ๋ชฉ๋ก)
# (2) generics.CreateAPIView (์์ฑ)
# (3) generics.RetrieveAPIView (1๊ฐ)
# (4) generics.UpdateAPIView (1๊ฐ ์์ )
# (5) generics.DestroyAPIView (1๊ฐ ์ญ์ )
# (6) generics.ListCreateAPIView (์ ์ฒด ๋ชฉ๋ก + ์์ฑ)
# (7) generics.RetrieveUpdateAPIView (1๊ฐ + 1๊ฐ ์์ )
# (8) generics.RetrieveDestroyAPIView (1๊ฐ + 1๊ฐ ์ญ์ )
# (9) generics.RetrieveUpdateDestroyAPIView (1๊ฐ + 1๊ฐ ์์  + 1๊ฐ ์ญ์ )
```

- ๊ทธ๋์ ์ฐ๋ฆฌ๊ฐ ์์ ๋ง๋  5๊ฐ์ง์ ๊ธฐ๋ฅ์ generics๋ก ๋ง๋ ๋ค๋ฉด, 6๋ฒ๊ณผ 9๋ฒ์ ์ฌ์ฉํ๋ฉด ๋๋ค.

```python
from rest_framework import generics

class BooksAPIGenerics(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

class BookAPIGenerics(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    lookup_field = 'bid'
```

- **์์ ๊ฐ์ด ์ฌ๋ฌ ๊ฐ์ mixins๋ฅผ ๊ฐ๊ฐ ์์ํ๋ ๋ฐฉ์์์ ์ด์ ๋ mixins๊ฐ ์กฐํฉ๋ generics๋ฅผ ํ ๋ฒ๋ง ์์๋ฐ๋ ๊ฒ์ผ๋ก ๋๋๋ค.** 
  - ex) class BookAPIGenerics(generics.RetrieveUpdateDestroyAPIView):
- ๊ทธ๋ฐ๋ฐ return ๊ตฌ๋ฌธ๋ ์์ ์ฌ๋ผ์ก๋ค. ์์ ์ฌ๋ฌ ๊ฐ์ mixins๋ฅผ ์ฌ์ฉํ๋ ๋ฐฉ์์์๋ ๊ฐ mixins๋ฅผ ๋ฉ์๋์ ๋งค์นญ์ํค๋ ๊ณผ์ ์ด ํ์ํ๊ณ  ๊ทธ๊ฒ return ๊ตฌ๋ฌธ์ ์์๋ค. 
- **์ด์ ๋ mixins๊ฐ ์กฐํฉ๋ generics๋ฅผ ์ฌ์ฉํ๋ค ๋ณด๋ -> ์ด์ฐจํผ List + Create ์ด๋ฉด GET๊ณผ POST ๋ฉ์๋๊ฐ ์ฌ์ฉ๋๋ค๋ ๊ฒ์ ์ด๋ฏธ ์๊ณ  ์๋ ๊ฒ์ด๋ค.**
  - ์ค์ ๋ก DRF ๋ด๋ถ์ generics.ListCreateAPIView์ ์ฝ๋๋ฅผ ๋ณด๋ฉด, ๋ด๋ถ์ def get / def post๊ฐ ์ ์๋์ด์๋ค.

- **๊ฒฐ๊ตญ, generics๋ mixins๋ฅผ ์กฐํฉํ๊ธฐ๋ง ํด๋์ ์ฝ๋๋ผ๊ณ  ํ  ์ ์๋ค.**


<br>

### DRF Viewset๊ณผ Router
- generics๋ง์ผ๋ก ์ถฉ๋ถํ ์ฝ๋๋ฅผ ๊ฐ์ํํ์ง๋ง, ๋ ์ค์ผ ์ ์๋ ๋ถ๋ถ์ด ์๋ค. 
- ์ง๊ธ๊น์ง๋ ํ๋์ ํด๋์ค๊ฐ ํ๋์ URL๋ฅผ ๋ด๋นํ๋ ๋ฐฉ์์ด์๋ค. URL๋ง๋ค ํด๋์ค๋ฅผ ๋ง๋ค๊ณ  ์ฒ๋ฆฌํ  ์ ์๊ฒ ํ๋ค.
- **๊ทธ๋ฌ๋ค๋ณด๋ queryset๊ณผ serializer_class ๋ถ๋ถ์ด ๊ฒน์น๊ฒ ๋์๋ค. ๊ทธ๋์ ํ๋์ ํด๋์ค๋ก ํ๋์ ๋ชจ๋ธ์ ์ ๋ถ ์ฒ๋ฆฌํด์ค ์ ์๋ค๋ฉด ๊ฒน์น๋ ๋ถ๋ถ์ด ์ฌ๋ผ์งํ๋ฐ ์ด๊ฒ ๋ฐ๋ก Viewset์ด๋ค.**
  - Viewset์ ๋ง๊ทธ๋๋ก View์ ์งํฉ์ด๋ค. ๊ธฐ๋ณธ์ ์ธ ๋ชจ์ต์ ํด๋์คํ View์ ๊ธฐ๋ณธํ๊ณผ ๊ฐ๋ค. 

```python
from rest_framework import viewsets

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

- ์ด๋ ๊ฒ Book ๋ชจ๋ธ REST API๊ฐ 4์ค๋ก ์ค์ด๋ค์๋ค.
- **ModelViewSet๋ฅผ ์์๋ฐ์ ํด๋์ค๋ฅผ ๋ง๋ค๊ณ  queryset๊ณผ serializer_class๋ฅผ ์ค์ ํด์ฃผ๋ฉด ๋ชจ๋ธ์ ๋ํ ๊ธฐ๋ณธ์ ์ธ REST API๊ฐ ์์ฑ๋๋ค.**
  - ์ด๋ฌํ ModelViewSet์ ๋ด๋ถ ๊ตฌ์กฐ๋ฅผ ๋ณด์.

```python
class ModelViewSet(mixins.CreateModelMixin, mixins.RetrieveModelMixin, mixins.UpdateModelMixin, mixins.DestroyModelMixin, mixins.ListModelMixin, GenericViewSet):
...
```

- **์ด๋ ๊ฒ DRF ๋ด๋ถ์์ ModelViewSet๋ฅผ ์ฐพ์๋ณด๋ฉด, ViewSet๋ ๊ฒฐ๊ตญ์ Mixin์ ์ฌ์ฉํ๊ณ  ์๋ค๋ ๊ฒ์ ์ ์ ์๋ค. ๊ฒฐ๊ตญ์๋ ์์ ๋ฐฐ์ด mixin์ ๊ธฐ๋ฐ์ผ๋ก ์์ฑ๋์๋ค.**

- **์ด๋ฌํ ViewSet์ URL๋ก ์ฐ๊ฒฐํ  ๋๋ ์กฐ๊ธ ๋ค๋ฅด๋ค.**

```python
from rest_framework import routers
from .views import BookViewSet

router = routers.SimpleRouter()
router.register('books', BookViewSet)

urlpatterns = router.urls
```

- **๋ผ์ฐํฐ ๊ฐ์ฒด๋ฅผ ๋ง๋ค๊ณ  ๋ผ์ฐํฐ์ ์ฐ๋ฆฌ์ ViewSet๋ฅผ ๋ฑ๋กํ๋ค. ๊ทธ๋ฆฌ๊ณ  urlpatterns์๋ router.urls๋ฅผ includeํด์ ์์ฑํ๋ฉด ๋๋ค.**

- **์ด๋ ๊ฒ ViewSet๊ณผ Router๋ฅผ ์ฐ๋ ๊ฒ์ผ๋ก ์ป์ ์ ์๋ ์ฅ์ ๋ค์ด ์๋ค.**
  - ํ๋์ ํด๋์ค๋ก ํ๋์ ๋ชจ๋ธ์ ๋ํ ๋ด์ฉ์ ์ ๋ถ ์์ฑํ  ์ ์๊ณ  queryset์ด๋ serializer_class ๋ฑ ๊ฒน์น๋ ๋ถ๋ถ์ ์ต์ํํ  ์ ์๋ค.
  - ๋ผ์ฐํฐ๋ฅผ ํตํด URL๋ฅผ ์ผ์ผ์ด ์ง์ ํ์ง ์์๋ ์ผ์ ํ ๊ท์น์ URL์ ๋ง๋ค ์ ์๋ค.

<br>

### SerializerMethodField
- **Serializer ํด๋์ค ๋ด๋ถ์ ๋ณ์๋ฅผ SerializerMethodField๋ก ์ถ๊ฐ๋ก ์ ์ํ๋ฉด, ํด๋์ค ๋ด๋ถ์ ์๋ ๋ฉ์๋๋ฅผ ํธ์ถํ๊ฒ ๋๋ค.**
- ๋ง์ฝ SerializerMethodField ์ธ์์ ์๋ฌด๊ฒ๋ ๋ฃ์ง ์๋๋ค๋ฉด โdef get_ํ๋์ด๋ฆโ ์ด๋ ๊ฒ get_ํ๋์ด๋ฆ์ ๋ฉ์๋๋ฅผ ํธ์ถํ๊ฒ ๋๋ค. ๊ทธ๋์ ๊ทธ ํธ์ถํ ๋ฉ์๋์ ๊ฐ์ ๊ฐ์ ธ์ฌ ์ ์๊ฒ ๋๋ค.
  - ํด๋น ๋ฉ์๋๋ self์ธ์๋ obj๋ผ๋ argument๋ฅผ ๋ฐ๋๋ค. obj๋ ์ง๋ ฌํ๋๋ ๊ฐ์ฒด๋ฅผ ์๋ฏธํ๋ค.

- [์ฐธ๊ณ  ๋ธ๋ก๊ทธ](https://velog.io/@oen/SerializerMethodField-%EA%B3%B5%EC%8B%9D%EB%AC%B8%EC%84%9C-%EB%B2%88%EC%97%AD)

<br>

### View์์ permission_classes๋ก ์ธ์ฆ๊ณผ ๊ถํ ์ค์ ํ๊ธฐ
```python
from rest_framework.permissions import IsAuthenticated
class ExampleView(APIView):
   permissions_classes = [IsAuthenticated]
   
   def get(self, request, format=None):
       content = {'status' : 'request was permitted'}
       return Response(content)
```

- [๊ด๋ จํด์ ์ข์ ๋ด์ฉ์ ๋ธ๋ก๊ทธ](https://donis-note.medium.com/django-rest-framework-authentication-permission-%EC%9D%B8%EC%A6%9D%EA%B3%BC-%EA%B6%8C%ED%95%9C-cc9b183fd901)

<br>

### ์ฟผ๋ฆฌ์คํธ๋ง์ ๋ฐ์ ์ ์๋ URL ๋ง๋ค๊ธฐ
- https://eunjin3786.tistory.com/274

<br>

### Serializer์์ filter ํจ์ ์ฌ์ฉํ๊ธฐ
```python
class GuNameModelSerializer(ModelSerializer):
...
rainfall_data = serializers.SerializerMethodField()
sewer_pipe_data = serializers.SerializerMethodField()

    def get_rainfall_data(self, obj):
        rainfalls = obj.rainfall
        datetime_info = self.context["datetime"]
        rainfall_serializer = RainfallModelSerializer(rainfalls.filter(
            receive_time__gte=datetime_info,
            receive_time__lt=datetime_info + datetime.timedelta(minutes=10)
        ), many=True)
        return rainfall_serializer.data

    def get_sewer_pipe_data(self, obj):
        sewer_pipe = obj.sewer_pipe
        datetime_info = self.context["datetime"]
        sewer_pipe_serializer = SewerPipeModelSerializer(sewer_pipe.filter(
            mea_ymd__gte=datetime_info,
            mea_ymd__lt=datetime_info + datetime.timedelta(minutes=1)
        ), many=True)
        return sewer_pipe_serializer.data
```
- receive_time์ Rainfall ๋ชจ๋ธ์ ํ๋๋ก, receive_time__gte๋ greater than or equal์ ์ฝ์๋ก >=๋ฅผ ์๋ฏธํ๋ค. receive_time__lt์ less than์ผ๋ก < ๋ฅผ ์๋ฏธํ๋ค.
- datetime.timedelta(minutes=10)๋ 10๋ถ์ ์๋ฏธํ๋ ์ฝ๋์ด๋ค.
- **filter() ๋ผ๋ ํจ์์์ ์กฐ๊ฑด์ ์ฌ๋ฌ ๊ฐ ๋๊ณ  ์ถ์ผ๋ฉด, filter(A์กฐ๊ฑด, B์กฐ๊ฑด) ์ด๋ ๊ฒ ํด์ฃผ๋ฉด ๋๋ค.**
  - [๊ด๋ จ ๋ธ๋ก๊ทธ](https://django-orm-cookbook-ko.readthedocs.io/en/latest/and_query.html)

<br>

### Serializer ํ๋ ์ค์ 
- **read_only_fields ์ฌ์ฉํ๊ธฐ**
  - Serializer์ class Meta์ read_only_fields๋ฅผ ์ค์ ํ๋ฉด, ํด๋น ํ๋๋ ๋ฐ์ดํฐ๋ฅผ ์ ์กํ๋ ์์ ์ ํฌํจ์ํค์ง ์๋๋ก ์ค์ ํ  ์ ์๋ค.
```python
class CommentSerializer(serializers.ModelSerializer):

    class Meta:
        model = Comment
        fields = '__all__'
        read_only_fields = ('article',)  # article ํ๋๋ ๋ฐ์ดํฐ ์ ์ก ์ ์ ์ธ
```

- **Serializer ํ๋์ required=False ์ค์ **
  - **Serializer์ ์์ฑํ ํ๋์ required=False๋ผ๋ ์ค์ ์ ์ถ๊ฐํ๋ฉด, ํด๋น ํ๋๊ฐ ๋น์ด์๋ None ์ํ๋ผ๊ณ  ํ๋๋ผ๋ error๊ฐ ๋ฐ์ํ์ง ์๊ฒ ๋๋ค.**
```python
class CommentSerializer(serializers.Serializer):
    user = UserSerializer(required=False)  # May be an anonymous user.
    edits = EditItemSerializer(many=True)  # A nested list of 'edit' items.
    content = serializers.CharField(max_length=200)
    created = serializers.DateTimeField()
```

- ์์ ๊ฒฝ์ฐ, user๋ผ๋ ํ๋๊ฐ required=False๋ผ๋ ์์ฑ์ด ์๊ธฐ ๋๋ฌธ์ User๊ฐ ๋ก๊ทธ์ธํ์ง ์์๋, ๋น์ด์์ด๋ error๊ฐ ๋ฐ์ํ์ง ์๊ฒ ๋๋ค.

<br>

### View์์ partial ์ค์ 
- **View์์ Serializer๋ก ๊ฐ์ฒด๋ฅผ ๋ณด๋ผ ๋, ์ ์ํ ์ ์ฒด ํ๋๊ฐ ์๋ ๋ถ๋ถ์ ํด๋นํ๋ ํ๋ ๊ฐ๋ง ์๋ฐ์ดํธ๋ฅผ ํ๊ณ  ์ถ๋ค๋ฉด partial์ด๋ argument๋ฅผ ์ฌ์ฉํ๋ฉด ๋๋ค.**

```python
class AccountBooksRecordDetailAPIView(APIView):
  ...
  def put(self, request, record_id):
      record = self.get_object_and_check_permission(record_id)
      ...
       serializer = AccountBooksRecordModelSerializer(record, data=request.data, partial=True)
       serializer.is_valid(raise_exception=True)
       serializer.save()
```

- PUT ๋ฉ์๋๋ฅผ ์ฌ์ฉํ๊ธฐ ์ํด put ๋ฉ์๋๋ฅผ ๊ตฌ์ฑํ  ๋, ์ธ์๊ฐ์ธ id๋ก ์กฐํํ record ๋ณ์๋ฅผ AccountBooksRecordModelSerializer๋ก ๋ณด๋ด๋ ๊ฒฝ์ฐ ์๋ ฅ๋ ๋ฐ์ดํฐ๋ก ์์ ํ๋ ๊ฒฝ์ฐ์ด๋ค.
  - ์๋ ฅ๋ ๋ฐ์ดํฐ๋ฅผ request.data๋ก ํํํ๊ณ  ๋ชจ๋  ํ๋๊ฐ์ด ์์ ๋๋ ๊ฒ ์๋๊ธฐ์ partial=True๋ผ๋ ์ต์์ ์ถ๊ฐ.

* * *

### ํ์๊ฐ์๊ณผ ๋ก๊ทธ์ธ APIView ์์
- ํ์๊ฐ์ ๋ฐ ๋ก๊ทธ์ธ APIView
```python
from django.contrib.auth import authenticate, get_user_model, login
from drf_yasg.utils import swagger_auto_schema
from rest_framework import status
from rest_framework.permissions import AllowAny
from rest_framework.response import Response
from rest_framework.views import APIView

from config.permissions import IsOwner
from user.serializers import MyTokenObtainPairSerializer, SignInSerializer, SignUpSerializer, UserSerializer

User = get_user_model()

class SignUpView(APIView):
    permission_classes = [AllowAny]
    serializer = SignUpSerializer

    @swagger_auto_schema(request_body=SignUpSerializer)
    def post(self, request):
        serializer = self.serializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            res = Response(
                {
                    "message": "ํ์๊ฐ์์ ์ฑ๊ณตํ์ต๋๋ค.",
                },
                status=status.HTTP_201_CREATED,
            )
            return res
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class SignInView(APIView):
    permission_classes = [AllowAny]
    serializer = SignInSerializer

    @swagger_auto_schema(request_body=SignInSerializer)
    def post(self, request):
        user = authenticate(
            request,
            email=request.data.get("email"),
            password=request.data.get("password"),
        )
        if not user:
            return Response({"error": "์ด๋ฉ์ผ ๋๋ ๋น๋ฐ๋ฒํธ๋ฅผ ์๋ชป ์๋ ฅํ์ต๋๋ค."}, status=status.HTTP_404_NOT_FOUND)
        login(request, user)
        token = MyTokenObtainPairSerializer.get_token(user)
        res = Response(
            {
                "message": f"{user.username}๋ ๋ฐ๊ฐ์ต๋๋ค!",
                "token": {
                    "access": str(token.access_token),
                    "refresh": str(token),
                },
            },
            status=status.HTTP_200_OK,
        )
        return res
```
- **get_user_model() ํด๋์ค**
  - ์์์๋ User = get_user_model()๋ก ์ฌ์ฉ๋์๋ค.
  - from django.contrib.auth import get_user_model ์ด๋ ๊ฒ import๋ฅผ ์งํํ๊ณ  ์ฌ์ฉํ๋ฉด, **get_user_model()์ django.contrib.auth.models.User๋ฅผ ๊ฐ๋ฆฌํจ๋ค. ์ฆ, User ๋ชจ๋ธ์ ์๋ฏธํ๋ค.**
  - ๊ทธ๋ฆฌ๊ณ  custom์ผ๋ก User๋ฅผ ๋ฐ๋ก ๋ง๋ค์ด๋ get_user_model()์ ์๋กญ๊ฒ ๋ง๋  User๋ฅผ ์ก๋๋ค. 
  - ๋ํ, ํด๋น ํด๋์ค๋ฅผ ์ฌ์ฉํ๋ ค๋ฉด **settings.py์์ AUTH_USER_MODEL = "user.User"** ์ด๋ ๊ฒ ์ค์ ํด์ค๋ค.
    - user๋ผ๋ ์ด๋ฆ์ app์ ์๋ models.py์ User ๋ชจ๋ธ์ ์๋ฏธ
  - [๊ด๋ จ ๋ธ๋ก๊ทธ](https://han-py.tistory.com/353)

- **SignInView์์ authenticate ํจ์**
  - user = authenticate(request, email=request.data.get("email"), password=request.data.get("password"),) ์ด๋ ๊ฒ authenticate ํจ์๋ฅผ ์ฌ์ฉํด์ JSON ๋ฐ์ดํฐ๋ก ์๋ ฅ๋ ์ด๋ฉ์ผ๊ณผ ํจ์ค์๋๋ก ์ธ์ฆ๊ณผ์  ์งํ

- **token = MyTokenObtainPairSerializer.get_token(user)**
  - login ํ ์์ ์ฝ๋๋ก refresh_token๋ฅผ ๋ฐํํด์ฃผ๋ ์ ์ฐจ
  - Response์์ str(token) ์ด๋ ๊ฒ refresh_token์ด ๋๊ณ , str(token.access_token) ์ด๋ ๊ฒ access_token์ด ๋๋ค.

* * *

### ํ์๊ฐ์๊ณผ ๋ก๊ทธ์ธ Serializer ์์
- ํ์๊ฐ์๊ณผ ๋ก๊ทธ์ธ Serializer ์์(์์ APIView์ ์ฐ๊ฒฐ)

```python
from django.contrib.auth import get_user_model
from rest_framework import serializers
from rest_framework_simplejwt.serializers import TokenObtainPairSerializer

User = get_user_model()

class MyTokenObtainPairSerializer(TokenObtainPairSerializer):
     @classmethod
     def get_token(cls, user):
        token = super().get_token(user)
        return token

class SignUpSerializer(serializers.ModelSerializer):
     class Meta:
        model = User
        fields = (
            "email",
            "username",
            "password",
            "mobile",
        )
     
     def create(self, validated_data):
        email = validated_data.get("email")
        password = validated_data.get("password")
        username = validated_data.get("username")
        mobile = validated_data.get("mobile")
        user = User(email=email, password=password, username=username, mobile=mobile)
        user.set_password(password)
        user.save()
        return user

class SignInSerializer(serializers.Serializer):
    email = serializers.EmailField(max_length=100, write_only=True)
    password = serializers.CharField(max_length=128)
```    

- **simplejwt์ TokenObtainPairSerializer ์์๋ฐ์ ์ฌ์ฉํ๊ธฐ**
  - from rest_framework_simplejwt.serializers import TokenObtainPairSerializer ์ด๋ ๊ฒ import๋ฅผ ํ ๋ค, **TokenObtainPairSerializer๋ฅผ ์์๋ฐ์ ํ ํฐ์ ๋ฐํํ  ์ ์๋ serializer๋ฅผ ์๋กญ๊ฒ ์์ฑ**
  - **TokenObtainPairSerializer๋ ๋ํ TokenObtainSerializer๋ฅผ ์์๋ฐ๊ณ  ์๋ค. TokenObtainSerializer ๋ด๋ถ์๋ get_token ์ด๋ผ๋ ๋ฉ์๋๊ฐ ์ ์๋์ด ์๋ค.**

- **@classmethod get_token ๋ฉ์๋**
  - TokenObtainSerializer ๋ด๋ถ์ ์ ์๋ get_token ๋ฉ์๋๋ฅผ ์ฌ์ฉํด์ ํ ํฐ์ ๋ฐํํ๊ธฐ
  - [@classmethod ๊ด๋ จ ๋ด์ฉ](https://wikidocs.net/16074)
  - get_token ๋ฉ์๋์ return ๊ฐ์ธ token์ด refresh_token์ด ๋๊ณ , token.access_token์ด access_token์ด ๋๋ค.

- **SignUpSerializer์ create ๋ฉ์๋ overrideํด์ ํ์๊ฐ์**
  - create ๋ฉ์๋๋ฅผ overrideํด์ email = validated_data.get("email") ์ด๋ ๊ฒ ์ ํจ์ฑ ๊ฒ์ฌ๊ฐ ์๋ฃ๋ ๊ฐ๋ค์ ๋ฐ์ User ๋ชจ๋ธ์ ๊ฐ์ฒด๋ฅผ ์์ฑํ๋ค.
  - set_password ๋ฉ์๋๋ ํด์๊ฐ์ผ๋ก ์ํธํํด์ ๋น๋ฐ๋ฒํธ๋ฅผ ๋ฃ์ด์ฃผ๋ ์ญํ ์ ํ๋ค.
  - ์ด๋ ๊ฒ ํด์ ํ์๊ฐ์์ด ์๋ฃ๋๋ค. 


* * *

### DRF ๊ด๋ จ ์ฝ์ด๋ด์ผ ํ  ๋ธ๋ก๊ทธ
- https://whatisthenext.tistory.com/126
- https://donis-note.medium.com/django-rest-framework-rest-api-%EC%84%9C%EB%B2%84%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-7d28b0cf730e
- [ํ์ดํ์ด DRF ๊ด๋ จ ๋ธ๋ก๊ทธ](https://blog.payhere.in/django-rest-framework-serializers/)


