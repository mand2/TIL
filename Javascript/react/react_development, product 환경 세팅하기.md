# development, product 환경 세팅하기

### Today's Goal : 환경변수 설정하기    
다른 환경(개발, 운영, 테스트)에 따라 각각의 환경변수 지정하기. Maven build를 할 때 profile 별로 다르게 프로퍼티를 설정하는 것과 같은 느낌? 

<br><br>

### 사용 이유

 **<u>개발모드와 배포모드에 따라</u>** test 서버 주소나 요청API의 주소가 다를 경우와 같이 **결과값이 다르게 나와야할 때**. 혹은 개발중인 코드에서 보여주지 말아야 할 에러 메시지가 있을 수도 있고, 캐싱의 여부가 다를 때. 단순하게 페이지의 내용을 다르게 하고 싶을 때.

CRA로 하거나, CRA를 쓰지 않고 개발한 경우에는 webpack과 babel 설정을 통해 환경에 따라 다른 프로퍼티를 갖도록 분기 설정 해줘야 하는 것 같다.

<br>



### 설정 example

보통 이러한 문제들을 해결하기 위해서, Node.js에서는 **NODE_ENV**라는 환경변수를 설정하여,

```javascript
if( process.env.NODE_ENV == 'production' ) {
	console.log("Production Mode");
} else if( process.env.NODE_ENV == 'development' ){
	console.log("Development Mode");
}
```

위와 같은 방식으로 모드에 따라 코드를 알맞게 분기한다. 반드시 **production**, **development**두 가지의 모드만 있을 필요는 없고, 개발자의 재량에 따라서 여러 가지 만들 수 있다.

**NODE_ENV**를 설정하는 방법은 POSIX 호환 운영체제의 경우,

```
$ export NODE_ENV=production
```

이런 식으로 환경 값을 설정할 수 있다.
Windows의 경우에는 Node.js를 구동할 때,

```
> set NODE_ENV=production
```

이런 식으로 환경 값을 설정할 수 있다.

* 설정파일(webpack.config.js)에 설정하거나

* 명령어에서 옵션을 주는 방법   

  > $ webpack --optimize-minimize --define process.env.NODE_ENV="'production'" (equal with) 
  >
  > $ webpack -p 

<br><br><br>

### 내가 참고한 방법: 커스텀 환경변수 설정하기

*참고로 CRA는 환경 별로 변수를 사용하기 위해 Dotenv(https://www.npmjs.com/package/dotenv) 모듈을 사용한다*.

1) Shell에서 일시적인 환경변수 설정하기

npm 스크립트를 실행할 때 환경변수를 설정하는 방법이다. OS 별로 조금씩 다르다. 다음은 각 OS별에 대한 예시이다.

```
set "REACT_APP_SECRET_CODE=abcdef" && npm start (Windows CMD)
($env:REACT_APP_SECRET_CODE = "abcdef") -and (npm start) (Windows Powershell)
REACT_APP_SECRET_CODE=abcdef npm start (Linux, macOS Bash shell)
```



**2) .env 파일을 이용한 환경변수 설정하기**

각 환경 별로 변수를 다르게 설정해 사용하고 싶으면 프로젝트의 루트 디렉토리에서 ‘.env’ 파일을 사용하면 된다. 다음의 파일명으로 환경변수가 관리된다.

```
.env: 기본 파일.
.env.local: .env를 덮어쓰는 파일. Test를 제외한 모든 환경에서 로딩된다.
.env.development, .env.test, .env.production: 각 환경 별 파일.
.env.development.local, .env.test.local, .env.production.local: 각각 env.* 를 덮어쓰는 파일.
```



각 npm 스크립트에 대해 각 파일들은 다음과 같은 **우선 순위**를 갖는다.

> npm start: .env.development.local > .env.develpment > .env.local > .env
>
> npm run build: .env.production.local > .env.production > .env.local > .env
>
> npm test: .env.test.local > .env.test > .env (위에 서술한 것처럼 .env.local은 로딩 대상이 아니다.)

<br><br>

### 커스텀 환경변수 예제

1) 테스트를 위해 .env.development 파일과 .env.production 파일을 생성 후 각각 환경변수를 설정한다.

[![img](https://2.bp.blogspot.com/-RMxp374Trxw/W2FqfdfxMVI/AAAAAAAABbQ/bjFUtjxZTKMuAW2gj45pa-lxNW7j7cA5QCLcBGAs/s400/%25EC%25BA%25A1%25EC%25B2%2598.PNG)](https://2.bp.blogspot.com/-RMxp374Trxw/W2FqfdfxMVI/AAAAAAAABbQ/bjFUtjxZTKMuAW2gj45pa-lxNW7j7cA5QCLcBGAs/s1600/%EC%BA%A1%EC%B2%98.PNG)

[![img](https://1.bp.blogspot.com/-BFI-gxrGGOQ/W2Fqgwkc29I/AAAAAAAABbU/32O0mZ3i0xELiWxe7rYGrao4cjmuM9PPgCLcBGAs/s400/%25EC%25BA%25A1%25EC%25B2%25981.PNG)](https://1.bp.blogspot.com/-BFI-gxrGGOQ/W2Fqgwkc29I/AAAAAAAABbU/32O0mZ3i0xELiWxe7rYGrao4cjmuM9PPgCLcBGAs/s1600/%EC%BA%A1%EC%B2%981.PNG)

<br>

2) 환경변수를 사용하는 쪽에서는 `process.env.환경변수명` 으로 값을 호출한다.

```react
class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">{process.env.REACT_APP_BACKEND_HOST}</h1>
        </header>
        <p className="App-intro">
          To get started, edit <code>src/App.js</code> and save to reload.
        </p>
      </div>
    );
  }
}
```

<br><br>

3) ‘npm start’와 ‘npm run build’의 결과 화면에 차이를 볼 수 있다.

[![img](https://1.bp.blogspot.com/-ByC8CO8CtLU/W2FqnDrdkVI/AAAAAAAABbY/IMJ3yRxC5-QKXSV4u4PILAcdqQzxC65NACLcBGAs/s400/%25EC%25BA%25A1%25EC%25B2%25983.PNG)](https://1.bp.blogspot.com/-ByC8CO8CtLU/W2FqnDrdkVI/AAAAAAAABbY/IMJ3yRxC5-QKXSV4u4PILAcdqQzxC65NACLcBGAs/s1600/%EC%BA%A1%EC%B2%983.PNG)

<그림 1. 개발환경 화면>

[![img](https://3.bp.blogspot.com/-qaSRE7tOr2Q/W2FqtYcNecI/AAAAAAAABbc/V25hRbgeo38-MJyjs8JuQzrzXVQbvcQcQCLcBGAs/s400/%25EC%25BA%25A1%25EC%25B2%25984.PNG)](https://3.bp.blogspot.com/-qaSRE7tOr2Q/W2FqtYcNecI/AAAAAAAABbc/V25hRbgeo38-MJyjs8JuQzrzXVQbvcQcQCLcBGAs/s1600/%EC%BA%A1%EC%B2%984.PNG)

<그림 2. 운영환경 화면>

커스텀 환경변수를 사용할 때 주의할 점은 환경변수의 이름이 반드시 **‘REACT_APP_’으로 시작해야 한다는 점**이다. ‘REACT_APP_’으로 시작하지 않은 환경변수는 무시한다.





<br><br>

### 단점

 CRA은 내부적으로 설정 되어있지만 한정적인 설정밖에 안되어 이용하기 불편하다는 평이 있음. 특히  `react native dotenv`는 [.env만 사용가능](https://github.com/zetachang/react-native-dotenv)하여 사용하지 않았다는 분이 계심,,

>  (ex) .env.staging 설정

<br>

환경 변수를 아래와 같이 `.env`에 전달 받는 경우도 있는데, ~~나는 이렇게까진 쓰지 않았다,,~~ 

```react
.env 
REACT_ENV=$REACT_ENV

package.json
“scripts”: {
 “start”: “REACT_ENV=staging && react-scripts start”
}
```



<br><br>

-----------------

##### 출처 URL

* [Jello's development blog](http://guswnsxodlf.github.io/production-with-webpack)

* [Lemonja 님 블로그](http://lemonja.blogspot.com/2018/08/reactjs-cra.html)

* [BoBeen Lee 님 블로그](https://engineering.huiseoul.com/react-prod-staging-dev-%ED%99%98%EA%B2%BD-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-8eb6bbccddfe)

  