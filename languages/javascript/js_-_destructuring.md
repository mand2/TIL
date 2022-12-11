# js\_구조분해할당\_destructuring

## 구조분해 할당

구조분해할당 관련 간단하게 나온 블로그 [URL](https://livelikeabel.tistory.com/31)

구조분해할당 관련 조금 자세한 설명 블로그 [URL](https://justmakeyourself.tistory.com/entry/javascript-object-destructuring)

## find ()

`find` 메서드는 `callback` 함수가 참을 반환 할 때까지 해당 배열의 각 요소에 대해서 `callback` 함수를 실행합니다. 만약 어느 요소를 찾았다면 `find` 메서드는 해당 요소의 값을 즉시 반환하고, 그렇지 않았다면 `undefined` 를 반환합니다.

\


```javascript
state = {
    notes : [
        {
            id : '_1aaaaaa',
            title : '끄아아ㅏㅏㅏㅏ',
            contents : '배가고프다아ㅏㅏㅏ'
        },
        {
            id : '2bbbbbbb',
            title : 'test2',
            contents : 'contents2'
        },
        {
            id : '3ccccc',
            title : 'test3',
            contents : 'cont33'
        }

    ],
    activeId: '3ccccc'
}
```

\
\
\


**ECMA 6**

```javascript
// 선택된 ListItem의 id 값인 객체를 note로 반환
const note = notes.find(
    (item) => item.id === this.state.activeId
)
```

**이전 JS ver.**

```javascript
function findNote(item) {
	return item.id === this.state.activeId;
}

console.log(notes.find(findNote));
```

\
\


\
\


## Event target() / currentTarget()

* event target 이벤트버블링의 가장 마지막에 위치한 **최하위 요소 반환**
* currentTarget 이벤트가 바인딩 된 요소(해당 요소) 반환

**예)**

```javascript
<div onclick = "check();">
    <span> test </span>
</div>

<script>
    function check(event) {
  		var ele = event.currentTarget;
  		console.log(ele);
	}
</script>
```

* target : span 반환
* currentTarget : div 반환

\
\


## Unit Test in JS

JS 에 대해 자세히 설명한 블로그 [URL](http://milooy.github.io/TIL/JavaScript/js-unit-test.html)
