# Store에서 왜 constructor를 쓸까

react 상태관리 라이브러리인 **mobx**를 사용할 때 Store를 쓴다. 각 Store를 전체 관리하는 rootStore를 만들면, 해당 rootstore를 constructor를 통해 불러오는데 왜 쓰는지 의문이 든다.

생각해보면 간단하다. 그냥 import 해버리면 같은 store를 또 생성해버리니까 자원낭비가 심해진다. 때문에 singleton 처리를 위해서 **constructor**를 통해 store를 불러온다.

```react
@Autobind
class Astore {
    constructor(root){
        this.root = root;
    }
}
```



<br><br>

### RootStore의 구조

```
RootStore
	|---- Astore
	|---- Bstore
	|---- Cstore
```

```react
import Astore from "";
import Bstore from "";
import Cstore from "";

class RootStore {
	constructor() {
		this.Astore = new Astore(this);
		this.Bstore = new Bstore(this);
		this.Cstore = new Cstore(this);
	}
}
export default RootStore;
```

이렇게 만든 후 각 component에서 필요한 store만 불러오는데, 이미 만들어져 있으면 그 store를 불러오기만 하면 된다..ㅎㅎ

