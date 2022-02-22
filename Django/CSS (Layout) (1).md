## CSS (Layout) (1)
- CSS (Cascading Style Sheets)
  - 각 HTML 엘리먼트에 대한 스타일을 기술
  - MSN 웹 문서 : https://developer.mozilla.org/ko/docs/Web/CSS


### 반응형 웹
- 예전에는 table element로 레이아웃을 잡았으나, 요즘에는 반응형 웹이 대세.
- 브라우저의 가로크기에 따라 각기 다른 CSS 스타일을 적용(다른 레이아웃을 적용)되는 웹페이지
- CSS Media Queries를 통해 구현 https://developer.mozilla.org/ko/docs/Web/CSS/Media_Queries/Using_media_queries

```css
/* 브라우저의 가로크기가 600px 이하일 경우, 아래 스타일이 적용 */
@media (max-width: 600px) {
    body {
        background-color: green;
    }
}
```

- 즉, 위의 코드는 최대 크기가 600px일 경우를 의미. 가로가 600px 이하일 경우 배경색이 green으로 변한다.


### 반응형 웹의 단점
- 예전에는 모바일/데스크탑 페이지를 따로 만드는 경우도 많았으나, 반응형 웹으로 구현하면 한 페이지에서 모바일/데스크탑 페이지를 대응 가능
  - 예외로 네이버는 따로 만든다. www.naver.com 과 m.naver.com 이렇게 구분되어있다.

- 모든 해상도 대응을 위한 CSS/이미지를 모두 불러와야 하므로, 로딩 시간 길어짐
- 복잡한 컨텐츠에는 맞지 않을 수도 있다. 레이아웃과 컨텐츠가 복잡하지 않아야, 일관된 UX을 제공할 수 있다.
  - 따로 분리하는 것이 더 나은 선택일 수도 있다.

- 기술이 중요한 것이 아니라 유지보수하기 좋고, 사용자가 이용하기 좋도록 개발해야 한다. 사용자는 반응형/적응형 웹이든 모바일/데스크탑 기기에서의 사용이 편리하면 OK











