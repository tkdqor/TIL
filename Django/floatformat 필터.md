## Floatformat 필터
- jdango에 기본적으로 내장되어 있는 필터로, template에서 바로 사용할 수 있는 반올림과 관련된 필터이다.


### {{ product.price|floatformat }}
- product 모델에서 price를 가져올 때, floatformat을 다음과 같이 붙여주면 소수점이 있는 숫자를 소수점 한 자리로 반올림 해준다. ex) 3.1457 -> 3.1
- 표시할 부분이 있는 경우에만 해당된다.  ex) 3.0000 -> 3


### {{ product.price|floatformat:3 }}
- floatformat에 숫자 정수를 붙여주면, 해당 정수만큼 소수점 자릿수로 반올림 해준다. ex) 3.14763 -> 3.148 / 3.0000 -> 3.000


### {{ product.price|floatformat:-3 }}
- floatformat에 음수를 인자로 두면, 해당 음수만큼 소수점 자릿수로 반올림 해주지만 표시할 소수점 부분이 없으면 표시되지 않는다. ex) 3.14763 -> 3.148 / 3.0000 -> 3


### {{ product.price|floatformat:0 }}
- floatformat에 0를 붙여주면, 가장 가까운 정수로 반올림을 하게 해준다. ex) 3.14763 -> 3 / 3.786 -> 4


### {{ product.price|floatformat:"0g" }}
- floatformat에 숫자 정수와 함께 g를 붙이고 따옴표 처리를 하게 되면, 천의 자리마다 콤마를 붙이게 된다. ex) 5800.26 -> 5,800
