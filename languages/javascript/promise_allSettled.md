#promise: 프로미스 한개가 실패해도 success로 처리하게 하기

## TL;DR

> 📌 Promise.allSettled()   
> **여러 프로미스를 병렬적으로 처리하되, 하나의 프로미스가 실패해도 무조건 이행**  
> javascript 2020 버전 feature 에 `promise.allSettled()` 라는 함수가 추가됨.  
> node.js 18 버전 가능. 12이하 지원불가.  
> ⇒ promise.any() 형태로, 가능하게 하려면 pollyfill 사용하기.



## Promise.allSettled이란?

- `Promise.allSettled`는 **여러 프로미스를 병렬적으로 처리하되, 하나의 프로미스가 실패해도 무조건 이행**한다.
- 아래 예시를 보면 promise2는 실패하는 프로미스지만, 결과적으로 catch로 넘어가지 않는 걸 볼 수 있다.

```javascript
const promise1 = Promise.resolve(3);// 성공const promise2 = Promise.reject('error');// 실패Promise.allSettled([promise1, promise2])
  .then(() => console.log('success'))
  .catch(() => console.log('error'));

// success
```

- 또한, 모든 프로미스가 실패해도 catch로 넘어가지 않는다.

```javascript
const promise1 = Promise.reject('error');// 실패const promise2 = Promise.reject('error');// 실패Promise.allSettled([promise1, promise2])
  .then(() => console.log('success'))
  .catch(() => console.log('error'));

// success
```

> ❓ Promise.allSettled로 즉시 치환할 수 있을까?

답은 NO. `Promise.allSettled`는 결과값 형태가 `Promise.all`과 다르다.
- `Promise.all`의 경우, 결과값이 array형태로 출력된다.
- `Promise.allSettled`는 결과값이 array object형태로 출력된다.

```javascript
const promise1 = Promise.resolve(3);
const promise2 = Promise.resolve(4);

Promise.all([promise1, promise2])
  .then((data) => console.log(data));

// [3, 4]
```


```javascript
const promise1 = Promise.resolve(3);
const promise2 = Promise.resolve(4);

Promise.allSettled([promise1, promise2])
  .then((data) => console.log(data));

// [ {status:"fulfilled", value:3}, {status:"fulfilled", value:4} ]
```

- 만약 Promise.all과 같은 형태의 데이터를 얻으려면 **추가 가공(`filter`, `map`)이 필요**하다.

```javascript
const promise1 = Promise.resolve(3);
const promise2 = Promise.resolve(4);

Promise.allSettled([promise1, promise2]).then((result) => {
  const fulfilled = result.filter(data => data.status === 'fulfilled').map(data => data.value);
  console.log(fulfilled);
});
```

---

## Promise.all의 경우, 하나의 프로미스라도 실패하면 에러로 처리되었다.

그래서 만일 나머지 요청은 문제없이 잘 됬는데, req3 요청만 에러가 생겨 reject되었다고 가정하자.그러면 다시 요청을 보내야 되는데, 또 req 1 ~ 5 개의 요청을 한꺼번에 보내야 된다. 실패한 req3 요청만 다시 보내서 응답 받으면 되지 비효율적으로 작업 처리를 하고 있는 것이다.

## Promise.allSettled()

배열에 status와 value 속성을 담은 객체를 반환.
이제 이 배열을 순회해서 값을 얻으면 되는 것이다.

![https://blog.kakaocdn.net/dn/lhoPb/btrE5z36Jqp/u0wU1dl6BM0XpTdUVvJKh1/img.png](https://blog.kakaocdn.net/dn/lhoPb/btrE5z36Jqp/u0wU1dl6BM0XpTdUVvJKh1/img.png)

### **Promise.allSettled 폴리필**

그렇다고 너무 Promise.allSettled 를 맹목적으로 믿고 사용하면 안된다.

Promise.all 의 상위호환이 아닌 편의성을 위해 구현된 기능이기 때문에 필요한 기능을 적절한 판단으로 적재적소에 사용하는게 좋다. 그리고 아직 표준에 등제된지 얼마되지 않은 따끈따끈한 기능이라, 
**구형 브라우저**나 **LTS 버젼 Node.js** 를 사용한다면 Pollyfill 를 따로 구현 해줘야 한다.
체크: [https://kangax.github.io/compat-table/es2016plus/](https://kangax.github.io/compat-table/es2016plus/)

JAVASCRIPT

```javascript
/* 폴리필 코드 */if(!Promise.allSettled) {  Promise.allSettled = function(promises) {    return Promise.all(promises.map(p => Promise.resolve(p).then(value => ({      status: 'fulfilled',      value    }), reason => ({      status: 'rejected',      reason    }))));  };}
```



출처:
- [Promise.allSettled?](https://mong-blog.tistory.com/entry/PromiseallSettled)
- [[JS] 📚 Promise.allSettled 와 Promise.all 비교 정리](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EB%8D%94%EC%9D%B4%EC%83%81-Promiseall-%EC%93%B0%EC%A7%80%EB%A7%90%EA%B3%A0-PromiseallSettled-%EC%82%AC%EC%9A%A9%ED%95%98%EC%9E%90)
- [https://dmitripavlutin.com/promise-all/Visit Website](https://dmitripavlutin.com/promise-all/)
- [https://twitter.com/mgechev/status/1300682318114361345](https://twitter.com/mgechev/status/1300682318114361345)  
    > Promise.any is not a standard just yet. Find polyfills here:
    > - [https://github.com/zloirock/core-js#promiseany…](https://t.co/uY8dodGMbw)
    > - [https://github.com/es-shims/Promise.any](https://t.co/ITvL6l5oCq)
