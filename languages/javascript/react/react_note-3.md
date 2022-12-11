# event handler 2

### EDIT 편집하기 이벤트 !

> 수정시 onChange 이벤트로 작성했기 때문에 입력하는 즉시 리스트 변환됨,,\
> 불필요한 렌더링 증가!\
> 수정버튼 따로 마련해서 수정 클릭 이벤트로 변환해야 렌더링 줄어들듯?
>
> 추가/삭제는 onClick 이벤트

\
\


#### - App > index.js

\#1 handleEditNote 추가

```javascript
// 편집
handleEditNote = (type, e) => {
    //새 notes어레이생성 = 현재 notes 배열을 반환
    const notes = [ ...this.state.notes ];

    // 선택된 ListItem의 id 값인 객체를 note로 반환
    const note = notes.find(
        (item) => item.id === this.state.activeId
    )

    note[type] = e.target.value ;

    this.setState({
        notes,
    });
}
```

\
\#2 메서드 넣기

```javascript
{
    notes.length !== 0 && 
        <Note 
    		note = {activeNote} 
    		onEditNote = { this.handleEditNote }
    	/>
}
```

\


#### - Note > index.js

onEditNote 또한 상속받아온다. change 이벤트가 실행되면 콜백함수로 리턴해버리기,\


```javascript
...
const { note, onEditNote } = this.props;
...
return(
    <div className="note">
    	<input className="title" value = {title} 
			onChange = { (e) => onEditNote('title', e) }
		/>
		<textarea className="note-contents" value = {contents} 
			onChange = { (e) => onEditNote('contents', e) }
		/>
	</div>
);
```

\


왼쪽 리스트에서 노트 클릭할 때마다 List 렌더링 발생,,,,

왜?

1. App\_index.js 클릭시 activeId 가 바뀐다(같은 note 눌러도 발생함,)
2. List\_index.js 로 넘어가서 바뀐 activeId 전달 이라고 생각했으나 onChange로 써서 그런거같음,, 변환해야할듯\~
