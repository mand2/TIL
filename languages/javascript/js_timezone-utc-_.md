**어제 날짜 timezone UTC로 설정하여 가져오기**

```javascript
const today = new Date()
const yesterday = new Date(today)

yesterday.setDate(yesterday.getDate() - 1)

today.toDateString()
yesterday.toDateString()


const yesterday = new Date();
yesterday.setDate(yesterday.getDate() - 1);

//Sun Oct 11 2020
console.log('yesterday.toDateString   ', yesterday.toDateString());

//Sun Oct 11 2020 16:41:27 GMT+0900 (GMT+09:00)
console.log('yesterday.toString       ', yesterday.toString());

//Sun, 11 Oct 2020 07:41:27 GMT
console.log('yesterday.toUTCString    ', yesterday.toUTCString());

//20201011
console.log(
    yesterday.getUTCFullYear() 
    + ('0'+(yesterday.getUTCMonth()+1)).slice(-2) 
    + ('0'+yesterday.getUTCDate()).slice(-2)
);

```

