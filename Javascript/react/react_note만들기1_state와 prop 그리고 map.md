# goal: state 와 props를 이용하여 컴포넌트에 주입
ECMA6부터 구조분해 가능하여 react에 구조분해 사용 + 화살표함수 사용.

### 1 App > index.js : state 사용
state? 동적인 데이터를 다룰 때 쓴다. 컴포넌트 내부에서 선언하고 내부에서 값 변경 가능(==직접 만들어서 쓴다), 

* 특: return() 선언 전에 변수를 구조분해하여 불러옴. 
* `<List />`에 불러온 notes 와 activeId 할당



### 2 List > index.js : props를 이용하여 App>index.js에서 상속
* 리액트에서의 반복문: map() 함수를 쓴다.
* 반복문을 돌 때에는 key가 필수
* id, title, contents 모두 받아옴.
* props 데이터는 읽기 전용 변경안됨.
* active 라는 값을 추가 > 사용자가 눌렀을 때와 아닐 때의 변경사항을 보여줘야하기때문. boolean으로 만든다.

### 3 ListItem > index.js : List > index.js 상속
* 3항 연산자를 이용하여 제목/내용이 있으면 해당 내용을, 없으면 제목/내용으로 표시
* list에서 설정한 active 값을 listitem 각각의 div에 classname으로 체크한다.
  how? 3항연산자 사용

```javascript
<div className={active? "list-item active" : "list-item"}>
```



