# event handler 1

this.setState() 는 state를 설정하는 메소드입니다. **state를 변경할 때는 반드시 this.setState()를 사용**하세요! this.state를 직접 조작하면, render() 메소드가 다시 호출되지 않습니다

* App>index.js  
  이벤트 핸들러메서드 작성, <list>에 클릭 이벤트시 관련 메서드를 호출해서 활성화하는(onListItemClick) 추가

  ```javascript
  //이벤트핸들러 메서드
  handleListItemClick = (id) => {
      this.setState({ activeId : id });
  }
  ```

  ```javascript
  <div className="container">
      <List 
  		notes={notes} 
  		activeId={activeId} 
  		onListItemClick={this.handleListItemClick}/> //method 전달
  	<Note />
  </div>
  ```

  

* List>index.js  
넘겨받은 이벤트메서드 (onListItemClick) 호출, ListItem에 넘겨주는데 이 때 onclick 이벤트로 작성해야 함. App>index.js에서 만든 이벤트핸들러 function 모습으로 parameter까지 넘겨준다.
  
  ```javascript
  const {
      notes, 
      activeId,
      onListItemClick // 추가됨
  } = this.props;
```
  
  ```javascript
  <ListItem 
  	key = {id}
  	id = {id}
  	active = {id===activeId}
  	title = {title}
  	contents = {contents}
  	
  	// 추가됨: ListItem에 메서드 전달위함
  	onClick = {() => onListItemClick(id)} 
  />
  ```



* ListItem > index.js

  ```javascript
  const { active,title,contents, onClick } = this.props;
  	return (
  	<div 
          className={active? "list-item active" : "list-item"} 
      	onClick = {onClick} //onClick 이벤트 발생 시 onClick() 호출한다.
  	>
          <div className="title">{title?title:'제목'}</div>
          <div className="list-item-contents">{contents?contents:'내용'}</div>
      </div>
  	);
  ```

  

`<ListItem />` 의 가장 상위 엘리먼트인 `<div /> ` 의 onClick 속성에 this.props.onClick을 작성했습니다. 그러면 유저가 해당 엘리먼트를 클릭했을 때 (즉, click 이벤트가 발생했을 때) 전달 받은 this.props.onClick을 호출합니다.
버튼 클릭(onClick), 인풋의 변경(onChange) 과 같은 유저의 상호작용에 의해 이벤트가 발생했을 때, 함수 형태의 이벤트 핸들러에 적절한 로직을 작성합니다. https://reactjs.org/docs/handling-events.html