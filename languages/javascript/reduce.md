# 비동기작업의 순차실행 (reduce)

### TL;DR

promise.all() 혹은 promise.allSettled() 가 아닌 promise객체 생성 + reduce 로 실행\
**비동기로 들어온 값**이지만, **순차적으로 실행해야 할 때** 사용할 수 있는 방법





참고 블로그 : [승택 오버플로우](https://seungtaek-overflow.tistory.com/4)



<figure><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&#x26;fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FW7Jcy%2FbtrkmNaiV2i%2F9KCydaMvNoGfljSa04w08k%2Fimg.png" alt=""><figcaption><p>순차실행</p></figcaption></figure>

## 기본 function

1초의 수행시간이 걸리는 비동기 작업을 다음과 같이 만들어보았다. 이 함수는 실행하고 1초 뒤에 _task finished!_라는 로그를 출력할 것이다.

```javascript
function handleTask(id) {
  return new Promise(resolve => {
    setTimeout(() => {
      console.log(`task${id} finished!`);
      resolve();
    }, 1000);
  })
}
```



## 순차실행

### forEach X

### reduce()

1. 기본\
   reduce 메소드의 초깃값으로 이행된 Promise를 넘겨주고(Promise.resolve()), tasks 배열을 돌며 이전 Promise의 이행을 기다렸다가 현재 task를 실행하는 새로운 Promise를 반환한다. 그리고 main 함수는 reduce 메소드의 최종 반환 값인 Promise의 이행을 기다린다.

```javascript
async function main() {
  const tasks = [1, 2, 3, 4, 5];

  await tasks.reduce((prevTask, currTask) => {
    return prevTask.then(() => handleTask(currTask));
  }, Promise.resolve());
}
```

2. 풀어쓴다면...\
   마치 다음 코드와 같은 Promise Chaining을 reduce 메소드를 통해 구현한 것이다.

```javascript
  await Promise.resolve()
    .then(() => handleTask(1))
    .then(() => handleTask(2))
    .then(() => handleTask(3))
    .then(() => handleTask(4))
    .then(() => handleTask(5))
```

3. &#x20;`await` : reduce 함수 내에 **async await 키워드**&#x20;

```javascript
async function main() {
  const tasks = [1, 2, 3, 4, 5];

  await tasks.reduce(async (prevTask, currTask) => {
    await prevTask;
    return handleTask(currTask)
  }, Promise.resolve());
}
```

\




### 응용

알람 메세지를 FIFO 로 순차적으로 실행해야 할 때로 가정

```javascript
const callApi: async(msg) => {  // 실제 순차적으로 실행하고자 하는 함수
    return await new Promise(async resolve => {
        totalMessages++;
        const res = await call();
        processedMessages++;
        resolve();
        await delay(10);
    }
}

const delay: async (sec) => {
    await new Promise(async res => await setTimeout(res, sec*1000));
}

const consumeMessages: async(messageList) => {
    await messageList.reduce(async (prev, msg) => { // prev 는 그전까지 있던 promises 들
        await prev; // 순차실행 완료될 때까지 대기
        return callApi(msg);    
    }, Promise.resolve());
    console.log(`successfully processed ${processedMessages} / ${totalMessages} `);
}
```











