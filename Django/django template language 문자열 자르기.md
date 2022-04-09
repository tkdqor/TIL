## django template language 문자열 자르기
- django template 언어를 사용할 때, 출력되는 문자열을 slice로 잘라서 사용할 수 있다.

```html
...
<p>{{ selected_problem.content|slice:":2" }}</p>
<p>{{ selected_problem.content|slice:"3:5" }}</p>
...
```

- 이렇게 출력되는 문자열을 잘라서 사용할 수 있다.
- 관련 블로그 : https://growd.tistory.com/10

