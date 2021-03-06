## app 내부의 templates 디렉토리 구성
- 많은 상품들의 정보를 한꺼번에 볼 수 있는 index.html
  - 로그인/회원가입 및 장바구니 버튼 생성 
  - 상품 검색하기 기능 해보기
  - 상품 전체 틀을 부트스트랩 - 그리드를 이용해도 될 것 같다...

- 하나의 상품에 대한 정보를 볼 수 있는 detail.html
  - 장바구니 및 구매하기 버튼 생성
  - 찜하기 기능 구현해보기(좋아요 기능처럼)

- 장바구니 화면을 볼 수 있는 cart.html
  - 구글에 bootstrap cart page 검색 -> 나오는 장바구니 페이지의 html과 css 가져와서 사용하기
  - 구글에 download arrow down icon 검색 -> 수량 +- 표시 아이콘으로 사용
  - 상품명과 가격, 옵션, 수량, 구매하기,  버튼 생성(구매하기 버튼은 클릭하면 checkout 페이지로 연결)

- 결제 화면을 볼 수 있는 checkout.html
  - 수령자 정보 입력 필요
  - cart.html에 있었던 상품 정보 그대로 가져오기
  - 결제 기능 구현 필요


## app 내부의 static 디렉토리 구성
- static -> app이름과 같은 디렉토리를 구성하고 css와 images로 나눠준다.

- 그리고 css의 경우, 메인 페이지는 main.css, 상품 상세 정보 페이지는 detail.css 이런식으로 한 페이지마다 구분지어서 설정하기.


## 부트스트랩 사용 시 주의사항
- 처음 부트스트랩 적용 시, html head 부분에 관련 링크를 붙여넣을 때, 내부의 css 파일보다 위쪽에다가 넣어주기
  - 컴퓨터가 코드를 해석할 때, 위에서부터 하기 때문에 해당 코드를 위쪽에 넣어주어야 한다.

- 자바스크립트 관련 코드는 body end tag 위에 넣어주면 된다.


## 데이터베이스 구성

**1. 사용자 User model**
- django가 제공하는 User 모델을 사용. models.py에서 from django.contrib.auth.models import User 라고 입력해주기.
- 기본적으로 제공하는 column를 그대로 사용.
- username / first_name / last_name / email


**2. 고객 Customer model**
- 고객에 대한 내용을 담은 model.
- User model과 1대1 관계가 되어야 한다. 유저 1명인데 고객이 2명일 수는 없기 때문이다.
- user / name / email
```python 
class Customer(models.Model):
    user = models.OneToOneField(User, null=True, blank=True, on_delete=models.CASCADE)    
    name = models.CharField(max_length=100, null=True)          
    email = models.CharField(max_length=100)

    def __str__(self):
        return f'{self.name}'
```
- **OneToOneField**란, 1:1관계를 의미한다. 주로 django에 기본적으로 정의되어 있는 User 모델과 설정해서 사용자와 관련된 정보를 저장하고 이를 확장해서 새로운 모델을 만들 때 사용하게 된다.  

- User모델과 1대1 관계를 설정해주자. null=True는 데이터가 없어도 상관없다는 의미.
- on delete를 사용해서 회원이 지워지면 고객도 지워지게 한다.
- name과 email은 CharField(character field) 사용


**3. 주문 Order model**
- Order model에서 주문자는 customer가 된다. 
- customer / date_ordered / complete / transaction_id(주문번호)
- Customer model과의 관계를 생각해보면 -> 고객 1명이 주문을 여러 개 만들 수가 있는 관계라는 점.
```python
class Order(models.Model):
    customer = models.ForeignKey(Customer, on_delete=models.SET_NULL, blank=True, null=True)
    date_ordered = models.DateTimeField(auto_now_add=True)
    complete = models.BooleanField(default=True, null=True, blank=True)
    transaction_id = models.CharField(max_length=200, null=True)

    def __str__(self):
        return f'{self.id}'
```
- customer column은 Customer 모델과 ForeignKey로 관계를 만들어주기. 그리고 customer가 삭제 되더라도 주문 정보는 남아있게끔 on_delete를 NULL로 처리.
- date_ordered는 주문된 날짜와 시간이고 자동으로 설정되게끔 auto_now_add를 설정.
- complete는 주문이 완료되었는지 아닌지를 표현해야 하기 때문에 Boolean 필드로 설정.
- transaction_id는 주문번호로 문자열로 지정하고 200자 제한으로 설정.



**4. 배송할 주소 Shipping Address model**
- Order 모델과 관련이 있고 이 모델에서는 누구한테 보내주고, 어떤 order인지 어떤 주소로 보내야하는지 나타내는 model이다.
- customer / order / address / city / state / zipcode / date_added
```python
class ShippingAddress(models.Model):
    customer = models.ForeignKey(Customer, on_delete=models.SET_NULL, blank=True, null=True)
    order = models.ForeignKey(Order, on_delete=models.SET_NULL, blank=True, null=True)
    address = models.CharField(max_length=200, null=True)
    city = models.CharField(max_length=200, null=True)
    zipcode = models.CharField(max_length=200, null=True)
    date_added = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f'{self.address}'
```
- 주문한 고객이 누군지, 어떤 주문인지, 주소와 도시 그리고 우편번호, 추가된 날짜에 대한 정보를 포함시키자.
- customer column은 Customer 모델과 ForeignKey로 관계를 만들어주기.
- order column 역시 Order 모델과 ForeignKey로 관계를 만들어주기.




**5. 주문된 상품 Order items model**
- Order model에서 주문된 상품이 어떤 상품인지 나타내는 model.
- product / order / quantity / date_added
```python
class OrderItem(models.Model):
    product = models.ForeignKey(Post, on_delete=models.SET_NULL, blank=True, null=True)
    order = models.ForeignKey(Order, on_delete=models.SET_NULL, blank=True, null=True)
    quantity = models.IntegerField(default=0, null=True, blank=True)
    date_added = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f'{self.product}'
```
- Order items 모델은 Order 모델과 관계가 있는데, Order 1개에 수많은 Order items가 있을 수 있다. 그래서 ForeignKey로 관계를 만들고 삭제 되더라도 남아있게끔 설정.
- 그리고 추가로, Product 모델과의 관계도 설정해야 한다. 판매되고 있는 product 중 장바구니에 담긴 product를 의미.
- quantitiy는 수량을 나타내는 column으로 Integer 필드로 설정.


**6. 판매되는 상품 정보 Products model**
- 현재 플랫폼에서 판매되고 있는 상품의 정보를 담고 있는 model.
- name / price / digital / image
```python
class Product(models.Model):
    product_name = models.CharField(max_length=100)
    brand = models.TextField()
    price = models.FloatField()             
    image = models.ImageField(null=True, blank=True)     
    created_at = models.DateTimeField()

    def __str__(self):
        return f'{self.product_name}: {self.brand}'
```
- price는 숫자니까 Float 필드 선택
- 이미지는 Image 필드 선택 -> 해당 과정에서 오류가 발생하는데 python -m pip install Pillow로 진행
- 관련 정보 https://healthdevelop.tistory.com/entry/Django-Cannot-use-ImageField-because-Pillow-is-not-installed-%EC%98%A4%EB%A5%98%ED%95%B4%EA%B2%B0 
- https://damio.tistory.com/50

* * * 
## null=True / blank=True의 차이점?
- models.py에서 필드를 정의할 때, null=True나 blank=True를 설정하게 된다.
- null=True의 경우는 -> 그 데이터의 값이 아예 없는 것을 의미한다. 따라서 해당 필드에 데이터가 아예 없어도 된다는 것을 의미한다.   
  반면 blank=True의 경우는 -> 데이터의 값이 비어있어도 된다는 것을 의미한다. 따라서 DB 해당 필드에 데이터가 ''이런식으로 비어있게끔 저장이 되어진다.
