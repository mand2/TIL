# react\_mobx\_counter play 2

#### mobx 사용하기 \_ counter play 2

```
-RootStore
 |---- stores
       |---- CounterStore 
       |---- MarketStore 
 |----components
       |---- Counter//CounterStore 가져옴
       |---- SuperMarket // SuperMarketTemplate 으로 반환
             |---- ShopItemList
                   |---- ShopItem
             |---- BasketItemList
                   |---- BasketItem
-index // 여기서 root Constructor 생성
-App

```

\
\


#### 세부참조는 최대한 늦게

변동이 일어날 수 있는 count 값의 세부참조를 우리는 BasketItem 컴포넌트 내부에서 하게 된다면, 더 높은 성능으로 컴포넌트를 업데이트 할 수 있습니다. 여기서 item.name 값은 바뀌지 않기 때문에 key 설정 부분에선 문제가 되지 않습니다.

**기존 BasketItemList**

```
const itemList = items.map(item => (
    <BasketItem
      name={item.name}
      price={item.price}
      count={item.count}
      key={item.name}
      onTake={onTake}
    />
));
```

**성능최적화로 바뀐 BasketItemList,,**

```
const itemList = items.map(item => (
    <BasketItem
      item={item}
      key={item.name}
      onTake={onTake}
    />
));
```

\


#### 함수는 미리 바인딩하고, 파라미터는 내부에서 넣어주기

**ShopItemList**

```
const ShopItemList = ({ onPut }) => {
  const itemList = items.map(item => (
    <ShopItem {...item} key={item.name} onPut = {onPut}/>
  ));
  return <div>{itemList}</div>;
};
```

\
\


#### 변경된 구조

```
-RootStore
 |---- stores
       |---- CounterStore 
       |---- MarketStore 
 |----components
       |---- Counter//CounterStore 가져옴
       |---- SuperMarket // SuperMarketTemplate 으로 반환
             |---- ShopItemList
                   |---- ShopItem
             |---- BasketItemList
                   |---- BasketItem
             |----TotalPrice
-index // 여기서 root Constructor 생성
-App
```

\
