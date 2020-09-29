# mobx 설치하기

여러번의 삽질 끝에 mobx 설치하기 완료!! ~~설정이 반이다~~ 라는 말이 있듯이 설정하는 데 시간이 오래걸렸다. 흑흑
<br>

### mobx-with-react라는 react 파일 만들기

```
$ cd D:\practice\react
$ npx create-react-app mobx-with-react
$ cd mobx-with-react
```
<br>

### 여러 설정들 yarn add 하고, git 커밋 후 `yarn eject` 하기

```
- path: /mobx-with-react
$ yarn add react-router-dom mobx mobx-react axios
$ git add ./
$ git commit -m "Save before ejecting"
$ yarn eject
```
<br>
git에 커밋하지 않으면 yarn eject 자체가 성립되지 않는다. 그 유명한 벨로퍼트님은 그냥 eject해야 bable 설정을 커스터마이징 할 수 있다 라고 쓰기만 하셔서,,, 찾느라 힘들었음. 

다른 [블로그](https://www.dnkdream.com/react-koa-blog-10/)(이 분은 더 mobx를 쓰시는 분인듯!!)에선 path가 `react` path에서 git을 커밋하면 된다는데 내 상황에선 안되더라 ㅠ_ㅠ 그래서 mobx-with-react폴더에 있는 .git 에 커밋해버림. 성공! 했다,, 

<br><br><br>

yarn eject 할 때 

> ? Are you sure you want to eject? This action is permanent.

라고 나오는데 **`y` 라고 누르면 된다**. 그 후 알아서 package.json에 설정됨!

### package.json에서 babel 설정 수정하기

```json
"babel": {
    "presets": [
        "react-app"
    ],
    "plugins": [
        ["@babel/plugin-proposal-decorators", { "legacy": true }],
        ["@babel/plugin-proposal-class-properties", { "loose": true }]
    ]
}
```

맨 아래쪽에 babel 설정이 명시되어 있는데 바벨 플러그인을 위의 형태로 바꿔준다.

<br>

### @babel 패키지 추가하기

```
- path: /mobx-with-react
$ yarn add @babel/plugin-proposal-class-properties @babel/plugin-proposal-decorators
```

<br>

그 후 시작을 하면 된다!

```
$ yarn start
```

는 안됨,,, 무슨일이지 ㅠㅠ

<br>



보니까 **typescript 모듈이 없다**고 나온다. ㅎ,, 왜냐면,,ㅋ,,, VS code에서 @와 같은 어노테이션을 쓸 때 클래스 오류가 생성되어서 `tsconfig.json`을 추가했더니 이거 때문에 생긴 일임..

그래서 일단 이설정파일을 지워봤다 > 그러니까 잘 됨!!

아마 이걸 넣고 실행하려면 이 [주소](https://velog.io/@hih0327/TypeScript-%EA%B8%B0%EC%B4%88-%EC%97%B0%EC%8A%B5)에서 잘 봐야할듯 

```
$ yarn init -y
$ npm install -g typescript / yarn add typescript 하던지,,
```

<br>



다음은 **webpack에 대해** 공부해야 할 듯하다. 블로그 바로가기: [URL](https://velog.io/@jeff0720/React-%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD%EC%9D%84-%EA%B5%AC%EC%B6%95%ED%95%98%EB%A9%B4%EC%84%9C-%EB%B0%B0%EC%9A%B0%EB%8A%94-Webpack-%EA%B8%B0%EC%B4%88)