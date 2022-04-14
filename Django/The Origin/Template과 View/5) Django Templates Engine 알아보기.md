## Django Templates Engine 알아보기
- 일반적인 웹에서는 웹 브라우저가 요청을 날리면 View에서 html, css, js 등 응답을 해서 브라우저는 그 html을 받아서 -> 브라우저가 그 html 파일을 랜더링하게 해준다.
- 그리고 View에서 Template으로 가면, Template Engine이 이 Template를 실행시키면서 template 언어를 번역해주기 때문에 View에서 응답할 때는, 이미 template 언어가 html 태그로 변환이 되서
  브라우저로 응답이 가게 된다.   
