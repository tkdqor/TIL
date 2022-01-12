# HTML 기초

## Web
- Web은 World Wide Web의 줄임말로 전세계적으로 인터넷에 연결된 컴퓨터를 통해서 사람들이 정보를 공유할 수 있는 거미줄처럼 엮인 공간을 의미.
- 이 웹이라는 공간에서 정보를 공유하기 위해 사용되는 가장 기본적인 단위는, 바로 **웹 페이지**이다.
  - 웹 페이지란, 정보를 공유하기 위해 정보에 대해서 서술한 하나의 문서라고 보면 된다.
- 또한, 하이퍼링크를 가진 텍스트를 특별히 Hypertext라고 부른다. 웹 페이지는 가장 대표적인 하이퍼텍스트 문서에 해당한다.
- 그리고 서로 관련있는 여러 개의 웹 페이지들을 의미 있게 묶어서 하나의 서비스로 제공하는 것이 바로 **웹 사이트**이다. 
  - 이러한 웹 사이트를 기반으로 운영되는 서비스가 바로 네이버, 구글, 페이스북, 인스타그램 등 우리에게 익숙한 서비스들이다. 이러한 서비스들은 수십 혹은 수백 개의 웹 페이지로 구성되어 있다.

## HTML
- 웹 페이지는 단순한 텍스트의 나열이 아니다. 하이퍼텍스트인 것과 그렇지 않은 것을 구분하고, 제목과 부제목 등 문서의 전반적인 구조를 어떻게 짜야 되는지를 전부 기술해야 한다.
- 이러한 목적을 위해 만들어진 것이 바로 **HTML**, Hyper Text Markup Language라는 것이고 하이퍼텍스트 문서를 만들기 위한 마크업 언어라는 뜻.
  - 마크업 언어란, 문서의 구조가 어떻게 짜여지는지 기술하기 위한 언어. 즉 HTML은 웹 페이지의 구조를 구성하기 위한 언어이다. 


## 웹 브라우저
- 이러한 HTML 코드를 해석해서 웹 페이지로 그려주고 하이퍼링크를 바탕으로 웹 서비스를 이용할 수 있게끔 해주는 프로그램이 바로 **웹 브라우저**이다.
  - 그래서 우리는 웹 서비스를 이용할 때, 항상 웹 브라우저를 통해서 웹 페이지를 우리 눈으로 보게 된다. 
  - HTML 코드가 웹 브라우저에 의해 해석되어 화면상에 그려질 때, 즉 **렌더링(서버로부터 HTML파일을 받아 브라우저에 표시되는 과정)** 이 될 때 웹 페이지가 우리 눈에 보여지게 되는 것이다.  

* * *

## HTML element
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LIKELION</title>
</head>
<body>
    HELLO sangbaek!
</body>
</html>
```

- 브라우저가 위와 같은 HTML 코드를 읽는 방식은 사람이 책을 읽는 방식과 동일하다. 왼쪽에서 오른쪽 방향으로 텍스트 한 문장을 읽은 다음에, 아래에 있는 그 다음 문장들을 순서대로 읽게 된다.
- HTML 파일의 첫 문장은 반드시 
```html
<!DOCTYPE html>
```
라고 적는다. 이 코드는 해당 문서가 HTML5 기반의 문서라는 것을 의미한다. 2014년에 가장 대표적인 이 5버전이 확립되었다. 현재 모든 현업 개발자들은 HTML5 버전을 사용.

- HTML element는 HTML을 구성하는 가장 기본적인 단위이다. 
```html
<p>HELLO WORKS!</p>
```
다음과 같은 HTML element가 있을 때 앞,뒤에 있는 것들을 tag라고 부른다. 이 tag의 이름이 HTML element의 이름이 된다. ex.) p element  
앞에 있는 건 start tag, 뒤에 있는 건 end tag로 구분.  
그리고 start tag와 end tag 사이에 HTML element가 실제로 나타내고자 하는 데이터를 넣게 되는데, 이를 content라고 한다.  
그래서 위의 코드를 통틀어서 모든 구성요소를 합쳐 element라고 부르게 된다.
```html
<br>
<img src="sample.jpg">
<input type="text">
```
다만, 이런식으로 content를 가지지 않고 start tag로만 구성된 HTML element도 있다.  
이러한 element를 **void element 또는 empty element** 라고 부른다. 
