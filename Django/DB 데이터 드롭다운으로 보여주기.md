## DB 데이터 드롭다운으로 보여주기
```html
 <!-- 지역 선택 및 등록 버튼 -->
            <div>
                <select id="select_photospot" name="photospot" required>
                    <option value="" disabled selected>포토스팟 선택</option>
                    {% for travel in travels %}
                    <option value="{{ travel.title }}">{{ travel.title }}</option>
                    {% endfor %}
                </select>
            
                <button id="write_submit" type="submit">등록</button>
            </div>
```

- select와 option element를 사용해서 드롭다운 만들기 가능
- option element에 disabled selected 속성을 추가하면 -> 해당 옵션은 처음부터 선택되어있고, 화살표를 눌러도 선택 항목에 없앨 수 있다.
