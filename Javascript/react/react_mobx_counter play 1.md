### mobx 사용하기 _ counter play 1

참고 블로그 URL [바로가기](https://velog.io/@velopert/MobX-2-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EC%97%90%EC%84%9C-MobX-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-oejltas52z)

> * mobx 로 상태 관리 시작-
> * inject 를 사용하여 store를 component에 주입해준다.
>
> src/index.js 에서 const로 'counter' 라 명명했기 때문에 inject 시 counter 라고 써줘야 한다.   
> CounterStore / counterStore 등 바로 store에서 주입  ㄴㄴ임 아직은.

<br>

```react
src\index.js
import { Provider } from 'mobx-react'; // MobX 에서 사용하는 Provider
import CounterStore from './stores/CounterStore'; // 방금 만든 스토어 불러와줍니다.

const counter = new CounterStore(); // 스토어 인스턴스를 만들고

ReactDOM.render(
  <Provider counter={counter}>
    {/* Provider 에 props 로 넣어줍니다. */}
    <App />
  </Provider>,
  document.getElementById('root')
);

serviceWorker.unregister();
```





<br><br>

#### src\stores\CounterStore.js

```react
import { observable, action } from 'mobx';

export default class CounterStore {
  @observable number = 0;

  @action increase = () => {
    this.number++;
  }

  @action decrease = () => {
    this.number--;
  }
}
```





<br><br>

#### src\Counter.js

```react
import React, { Component } from 'react';
import { observer, inject } from 'mobx-react';

//index.js 에서 counter로 설정했기 때문에 counter로 불러온다
@inject('counter') 
@observer
class Counter extends Component {
  render() {
    const { counter } = this.props;
    return (
      <div>
        <h1>{counter.number}</h1>
        <button onClick={counter.increase}>+1</button>
        <button onClick={counter.decrease}>-1</button>
      </div>
    );
  }
}

export default Counter;
```

