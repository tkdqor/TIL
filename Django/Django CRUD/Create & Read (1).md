## CRUD 웹페이지 만들기
- 먼저, template를 table element를 활용해서 구성해보기.
  - table 관련 MDN 사이트 참고 : https://developer.mozilla.org/ko/docs/Web/HTML/Element/table

- posts 앱 내부에 있는 templates -> posts -> index.html파일 열기. 지금은 ul, li element로 구성되어 있으니 table로 표현해보자.

```html
<h1>Posts</h1>

    <table>
        <tr>
          <td>John</td>
          <td>Doe</td>
        </tr>
        <tr>
          <td>Jane</td>
          <td>Doe</td>
        </tr>
    </table>
```

- 이렇게 table element를 추가해보자. 그러면 간단한 table이 생성된 것을 확인할 수 있다.

```css
table, td {
    border: 1px solid black;
}
```

- table에 대한 css도 위와 같이 설정한다. -> 모든 table element와 td element에 대해서 1px로 실선, 검은색 테두리를 주었다. 
- command + + 또는 - 로 웹페이지 비율을 조정해보면 테두리가 2줄이 생긴다. 셀에도 테두리가 있고, 전체 테이블 테두리 외곽에도 있다. 

```css
table, td {
    border: 1px solid black;
    border-collapse: collapse;
}
```

- 이렇게 border-collapse라는 property를 추가해주면 테이블이 실선 하나의 테두리로 바뀌게 된다. 사실은 collapse가 되서 중첩된 것이다.


### Table element
- table를 만들고 싶으면 먼저, **table element**를 작성한다. 
  - 그리고나서 **tr, table row는 각 행을 의미한다.** 행 한 줄을 작성하겠다고 한다면 tr element를 하나 추가해주면 된다. 테이블의 행이 몇 줄인지 파악할 수 있게 해준다.
  - 각 줄을 세로로 쪼개서 칸을 만들어줘야 한다. 그래서 tr element 내부에다가 -> **td element, 즉 table data의 약자로 한 row를 몇 개의 셀로 구성할지를 결정할 수 있다.**

- 우리가 일반적으로 table를 사용할 때는, 맨 위에 있는 행에다가 -> 어떠한 데이터가 아래에 쌓일지 그 데이터 자체를 설명하는 column의 제목, 필드의 제목을 정의한 다음에 하단에는 관련 데이터가 쌓이게 된다.
  - **그래서 이러한 부분을 명시적으로 표현하기 위해서 table의 첫번째 행에 해당하는 부분을 우리는 table의 header(<thead>)라고 부른다.** 
  - **그리고 이 table header 아래에 실제 데이터가 row by row로 들어갈 부분을 table body(<tbody>)라고 부른다.**
    
 ```html
  <h1>Posts</h1>

    <table>
        <thead>
            <tr>
                <th>First name</th>
                <th>Last name</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>John</td>
                <td>Doe</td>
              </tr>
              <tr>
                <td>Jane</td>
                <td>Doe</td>
              </tr>
        </tbody>      
    </table>
```  

    
<img width="167" alt="image" src="https://user-images.githubusercontent.com/95380638/153920726-7bbd138f-3025-49ab-bf48-20a5892f8c4d.png">
    
- 다음과 같은 table을 구성할 수 있다.
- 그리고 thead에 있는 element애서 td가 아닌, th 라는 element를 사용하게 되면, header를 위한 셀로 변경된다. 
  - th element가 사용된 부분을 보게 되면, 글씨가 조금 더 진하게 표시된 것을 확인할 수 있다.
    

    
### index.html 수정하기
    
    
    
333
    
    
    
    
