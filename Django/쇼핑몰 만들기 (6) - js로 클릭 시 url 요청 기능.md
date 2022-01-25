## 상품 1개 조회 화면에서 장바구니 버튼으로 장바구니 추가하기

### views.py 수정
```python
from django.http import HttpResponse, JsonResponse
...

# 장바구니 추가 기능
def updatedItem(request, post_id):
    return JsonResponse('Item added', safe=False)
```

- 이렇게 views.py에서는 JsonResponse를 추가로 import하고 updatedItem이라는 함수를 생성해준다.

- **JsonResponse**는 Json 형태의 response를 생성할 수 있게 해준다. 사용 시 **첫번째 파라미터는 딕셔너리**이어야 하는데(예: return JsonResponse({'aoo': 'two'}) ), 만약 딕셔너리 이외를 받게할 경우에는 두번째 파라미터를 **safe=False**라고 설정하면 된다. 따라서 위의 코드는 reponse될 때 'Item added'가 출력된다.


```python
...
urlpatterns = [
  path('update_item/', views.updatedItem, name='update_item'),
]  
```

- 그리고 post앱 디렉터리에 있는 urls.py에서 위에서 생성한 함수를 연결하는 url을 추가로 설정해주기. 그러면 브라우저에 "Item added"가 출력된다.


### js파일 수정 및 코드 생성
- 그리고 우리는 js파일을 통해 로그인 된 사용자의 경우, 장바구니 버튼을 누르면 로그인 된 사용자인 것을 알 수 있었다. 추가로 함수를 만들어서 장바구니 버튼을 클릭했을 때 -> 위에서 생성한 views.py의 updatedItem 함수에다가 product의 ID와 action을 전달해보자.

```javascript
var updatedBtns = document.getElementsByClassName('cart-update')

for(var i = 0 ; i < updatedBtns.length; i++){
    updatedBtns[i].addEventListener('click', function(){
        var postID = this.dataset.post
        var action = this.dataset.action
        console.log('postID:',postID , 'action:',action)
        console.log('사용자:',user)
        if (user === 'AnonymousUser'){
            console.log('로그인이 되지 않았습니다.')
        }else{
            updateUserOrder(postID,action)
        }
    })
} 

function updateUserOrder(postID,action){
    console.log('로그인 되었으니 정보를 보냅니다.')

    var url ='/posts/' + postID + '/update_item/'   // 우리가 urls.py에서 설정한 url를 입력
    fetch(url,{                                     // fetch()를 이용해서 정보를 views.py로 전달  
        method: 'POST',                             // 정보를 POST 방식으로 보내기 (장바구니를 생성하는 것이기 때문)
        headers:{
            'Content-Type':'application/json',  // json의 형태로 정보를 보내겠다는 것

        },
        body:JSON.stringify({'postID': postID, 'action': action})    // postID와 action를 JSON 형태의 정보로 보내주겠다는 의미                        
    })

    .then((response) =>{              // 데이터가 views.py로 잘 전달되었는지 확인하는 과정으로 views.py에서 return되는 response를 확인하는 것
        return response.json()
    })  
    .then((data) =>{              // 우리가 받은 response를 data라고 해주고, console에 출력 해주기   
        console.log('data:', data)
    })                                             
}
```

- 기존의 작성한 js코드를 수정하고 밑에다가 추가로 새로운 함수를 설정하는 것이다. 그래서 만약 유저가 비로그인 사용자가 아닐 경우, 우리가 밑에 생성한 함수를 실행시키라는 것이다.
- **새로 생성한 함수에서 url를 정의할 때, 상품 1개 조회 화면에서 장바구니 버튼을 클릭하기 때문에 url에 post_id(product_id) 값이 들어가야 한다. 이럴 때는 위에서 정의한 "postID"를 +로 연결시켜서 url를 설정해주면 된다.**

- 그런데 이렇게 POST방식으로 fetch를 이용해서 정보들을 views.py로 보낼 때, django에서는 csrf_token를 사용해야 한다. 
  - https://docs.djangoproject.com/en/3.2/ref/csrf/ 해당 공식 문서에 나와있는 코드를 base.html에 먼저 추가해야 한다. base.html의 head 사이에 -> <script></script> 사이에 해당 코드를 추가하자.

```html
<!-- 자바스크립트 코드 -->
    <script type="text/javascript">
        var user = '{{request.user}}'

        // 자바스크립트 csrf_token
        function getCookie(name) {
            let cookieValue = null;
            if (document.cookie && document.cookie !== '') {
                const cookies = document.cookie.split(';');
                for (let i = 0; i < cookies.length; i++) {
                    const cookie = cookies[i].trim();
                    // Does this cookie string begin with the name we want?
                    if (cookie.substring(0, name.length + 1) === (name + '=')) {
                        cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                        break;
                    }
                }
            }
            return cookieValue;
        }
        const csrftoken = getCookie('csrftoken');

    </script>
```

- 그리고 다시 js파일로 돌아와서, fetch 함수 내부의 headers에다가 csrf_token 관련 코드를 추가로 입력해주기.

```javascript
var url ='/update_item/'                  // 우리가 urls.py에서 설정한 url를 입력
    fetch(url,{                               // fetch()를 이용해서 정보를 views.py로 전달  
        method: 'POST',                       // 정보를 POST 방식으로 보내기 (장바구니를 생성하는 것이기 때문)
        headers:{
            'Content-Type':'application/json',  // json의 형태로 정보를 보내겠다는 것
            'X-CSRFToken': csrftoken            // POST방식으로 보내기 위해 csrf_token 설정

        },
        body:JSON({'postID': postID, 'action': action})    // postID와 action를 JSON 형태의 정보로 보내주겠다는 의미                        
    })
```

- 이렇게 js파일을 설정해주면 다음과 같이
<img src="https://user-images.githubusercontent.com/95380638/150930179-0de6c257-18ab-4c35-9004-cfcb94dfe61b.png" width="70%" height="70%">

- 상품 1개 조회 화면에서 장바구니 버튼 클릭 시, post_id와 action를 JSON 형태의 데이터로 정보를 보내고 -> 설정한 url -> 설정한 views.py 함수에서 response 해준 것을 다음과 같이 console에 출력됨을 확인할 수 있다.



