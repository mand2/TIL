# 리액트에서 뒤로가기 버튼 따로 만들기

- react mobx 사용함. 
  - 뒤로가기버튼이 보일 component  자체에서 만들기
  - state 변경하는 클래스내에서 (store) 만들고 넘겨주기.

<br><br>

일단, DOM 개념부터 잘 알아야할 듯하다. ~~개념을 잘 몰라서 엄청 헤맸다~~.... 리액트라고 해도 자바스크립트를 기반으로 한 건데, '리액트만 있는 것'인줄 착각해서 검색부터 난관을 겪었다. 아무튼, DOM 내부에서 `history` 라는 객체에 페이지 이동 등이 스택으로 쌓인다. 

### history

>  `history` 는 자바스크립트 라이브러리로, 자바스크립트를 실행하는 순간부터 세션 히스토리를 쉽게 사용할 수 있도록 도와준다. 

<br><br>

### 1 뒤로가기버튼이 보일 component  자체에서 만들기

```react
@inject(...)

@observer
class Test extends Component{
    render(){
        const {store} = this.props;
        return (
            <div>
                <button className="btn" onClick={()=> this.props.history.goBack()}>
                	go BACK
                </button>
            </div>
        );
    }
}
```

`react-router-dom` 의 `BrowserRouter ` 와 같이 각 라우터 컴포넌트는 history 객체를 만든다. history객체는 현재 위치(`history.location`)와, 이전 위치를 스택으로 갖고있어 계속 추적 가능하다. 현재 location(위치)가 바뀌면 view가 리랜더링 되기 때문에 이전 history를 찾을 수 있다. 자세한 내용은 아래 원문 참고.

> Each router component creates a history object that keeps track of the current location (`history.location`) and also the previous locations in a stack. When the current location changes, the view is re-rendered and you get a sense of navigation. How does the current location change? The history object has methods such as `history.push()` and `history.replace()` to take care of that. `history.push()` is invoked when you click on a `<Link>` component, and `history.replace()` is called when you use `<Redirect>`. Other methods — such as `history.goBack()` and `history.goForward()` — are used to navigate through the history stack by going back or forward a page.



<br><br>

### 2 state 변경하는 클래스내에서 (store) 만들고 넘겨주기.

등록버튼을 누를 때 빈칸이 없을 때에만 `등록완료`가 뜬다고 가정. 알람은 다른 컴포넌트로 만들고, 컴포넌트로 넘겨줄 때 callback 형식으로 보내준다. 등록완료 확인버튼을 누르면 이전 페이지로 로딩하도록 구현하고자 할 때,

```react
@Autobind
class TestStore{
    input: {
        _name: '',
        _age: ''
    }
    
    check() {
        let check = true;
        _.forEach(this.result, ((value, key)=>{
            value = _.trim(this.result[key]);
            if(''=== value){ check=false; }
        }));
    }
    
    @actioin
    register() {
        if(this.check()){
            const{alert} = this.root.alertStore;
            alert('등록완료', ()=>{window.history.back();});
        }
    }
}
```

이 때에는 Window 객체를 이용해서 넘겨줌. store는 상속관계가 아니라 각 독립적인 관계라 ,,, view에서 보이는 부분이 아니기 때문에 this.props를 쓸 수가 없다.

### Window


> The DOM [`Window`](https://developer.mozilla.org/en-US/docs/Web/API/Window) object provides access to the browser's session history (not to be confused for [WebExtensions history](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/history)) through the [`history`](https://developer.mozilla.org/en-US/docs/Web/API/Window/history) object. It exposes useful methods and properties that let you navigate back and forth through the user's history, and manipulate the contents of the history stack.