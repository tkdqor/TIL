## Django Templates Engine 알아보기
- 일반적인 웹에서는 웹 브라우저가 요청을 날리면 View에서 html, css, js 등 응답을 해서 브라우저는 그 html을 받아서 -> 브라우저가 그 html 파일을 랜더링하게 해준다.
- **그리고 View에서 Template으로 가면, Template Engine이 이 Template를 실행시키면서 template 언어를 번역해주기 때문에 View에서 응답할 때는, 이미 template 언어가 html 태그로 변환이 되서 브라우저로 응답이 가게 된다. 즉, template를 서버에서 먼저 실행시킨다는 것이다. 브라우저는 번역된 html을 받아서 브라우저가 랜더링을 하는 것이다.**


### Template 태그
- block : 자식 템플릿으로 재정의할 수 있는 블록 / {% block name %} {% endblock %}
- extends : 부모 템플릿을 확장, 상속 / {% extends 'template_name' %}
- include : 템플릿을 로드하고 현재 Context로 렌더링, 템플릿 포함 / {% include template_name %}
- for : Context 변수의 배열의 항목을 반복 사용 / {% for variable in variable_list %} {% endfor %}
- if : 조건이 true이면 출력하고 아니면 미출력 / {% if bool %} {% endif %}
- url : 보기 및 선택적 매개변수와 일치하는 절대 경로 참조를 반환 / {% url 'url_name' %}


### Template 상속
- 부모 html을 자식 html들이 가져다가 쓰는 것. 부모 html의 공간에 자식 html이 들어가서 사용하는 것이다. 
- 그래서 header나 footer 등 중복되는 요소들을 상속을 받음으로서 줄일 수 있다.
- **프로그래밍은 무조건 반복을 줄이는 데 목적을 가지고 있다. 객체지향 프로그래밍도 마찬가지.**


### Template 필터
- date : 주어진 형식에 따라 날짜 형식을 지정 / {{ value|date:"D d M Y" }}
- default : 값이 False로 평가되면, 즉 값이 없을 때 지정된 기본값을 사용. 값이 있으면 값을 사용 / {{ value|default:"nothing" }}
- center : 주어진 너비의 필드에서 값을 가운데에 맞춘다. / {{ value|center:"15" }}
- truncatechars : 정의한 문자수보다 긴 경우 문자열을 자른다. 잘린 문자열은 번역 가능한 줄임표 문자 ("...")로 끝난다. / {{ value|truncatechars:7 }}
- intcomma : 정수 또는 부동 소수점(또는 둘 중 하나의 문자열 표현)을 세 자리마다 쉼표가 포함된 문자열로 반환 / {% load humanize %} {{ value|intcomma }}
- 공식 문서 : https://docs.djangoproject.com/en/4.0/ref/templates/builtins/#built-in-filter-reference


