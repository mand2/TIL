# @computed

```
@computed
get total(){
    console.log('총합계산 ,,,');
    return this.selectedItems.reduce((previous, current) => {
        return previous + current.price * current.count;
    }, 0);
};
```

> `@computed`란 observable에 의해 파생된 state.
>
> **computed** 값은 store 클래스 내에서 getter 함수로 표시된다. == 나중에 **기능을 호출하지 않고 속성으로 사용**할 수 있음
>
> 이제 action들이 제대로 들어가고 store가 완성되었습니다. 이제 우리는 이 store의 action들을 통해 state를 변형시킬 수 있게 됩니다.

벨로퍼트님의 정의::

## Computed Value (연산된 값)

연산된 값은, 기존의 상태값과 다른 연산된 값에 기반하여 만들어질 수 있는 값입니다. 이는 주로 \*\*`성능 최적화`\*\*를 위하여 많이 사용됩니다. 어떤 값을 연산해야 할 때, **연산에 기반되는 값이 바뀔 때만 새로 연산하게 하고, 바뀌지 않았다면 그냥 기존의 값을 사용 할 수 있게** 해줍니다.

이를 이해하기 위해 간단한 예시를 들어보겠습니다. 우리가 편의점에서 800원짜리 물을 네병 샀는데 이게 얼마나오지? 하고 체크하는 함수 `total()` 이라는 함수가 있다고 가정하겠습니다. 우리가 처음 머릿속으로 계산할때 암산으로 4 \* 8 에 32! 라면서 3,200 원이군, 하고 간단히 계산을 하겠죠. 잠시 후에 친구가 그거 다 얼마냐고 또 물어봅니다. 이 때 우리는 머릿속에서 별 생각안하고 응 3,200원이야 라고 말합니다. 친구가, 나도 한병 사줘! 하면 이때 다시 우리는 무의식중에 800원을 더해서 우리가 내야 할 돈이 4,000원인걸 연산합니다.

\
\


***

## REDUCE ()

reduce(callbackfn: (previousValue: U, currentValue: any, currentIndex: number, array: any\[]) => U, **initialValue: U**): U

If initialValue is specified, it is used as the initial value to start the accumulation. The first call to the callbackfn function provides this value as an argument instead of an array value.

Calls the specified callback function for all the elements in an array. The return value of the callback function is the accumulated result, and is provided as an argument in the next call to the callback function.
