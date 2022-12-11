# react 시작하기 + webpack + babel 설정

출처 URL: @noyo0123님 [바로가기](https://velog.io/@noyo0123/react-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-c1k2zrmxet)

내가 본 것들 중에서 가장 자세하게? 설명되어있는 것 같음. react를 쓰면서 설치해야할 모듈이나 모듈 구조에 대해 잘 정리되어 있다. 문제는 babel 설정을 어떻게 써야 하는지 자세히 나오질 않았다는 점,,!! 이전에 있던

\
\


### 기존구조 만들기

react는 결국에는 js파일입니다. 그 안에 html 마크업도 들어가고요, css도 다 들어간 형태라고 말할 수 있습니다.

### react를 사용환경 구축

npm를 통해 react를 환경을 구성하겠습니다. npm은 Node.js (자바스크립트코드가 브라우저가 아닌 곳에서도 동작할 수 있게 해준는 런타임 환경입니다.)에서 개발을 편하게 할 수 있도록 많은 사람들의 코드를 사용할 수 있게 해주는 프로그램이라고 생각하시면 됩니다.

npm은 Node.js를 설치하실때 같이 설치 됩니다.

가장 먼저 package.json을 만들기 위해 프로젝트 루트 디렉토리에서 터미널을 열고 다음과 같은 명령어를 입력합니다. npm init, yarn init 둘다 결국 결과는 같습니다. 저는 yarn을 사용하겠습니다.

```
yarn init -y
```

그 결과 package.json이 생성됩니다.

![image.png](https://images.velog.io/post-images/noyo0123/cf3238f0-0773-11ea-a2f2-4f505fa503a5/image.png)

react, react-dom 설치 설치시 package.json에 `name`프로퍼티의 공백문자나, 키워드가 같은 문자가 들어가면 에러나니 그 부분만 주의합니다.

```
yarn add react react-dom
```

react :리액트 라이브러리 react-dom :browser, dom, webapp 관리

### babel 설치

```
yarn add -D @babel/core babel-loader @babel/preset-env @babel/preset-react 
```

babel을 사용하는 이유는요. babel이 있으면 자바스크립트 코드가 어떤 환경에서 돌아갈지를 설정만 함으로써 코드를 트랜스파일 할 수 있습니다.

* @babel/core : 리액트는 es6를 사용하므로 여러 브라우저에서 사용가능하도록 es5이하 버전으로 syntax를 변경해줍니다.
* @babel/preset-react : jsx를 쓸 수 있습니다. jsx는 react 공식홈페이지 나옵니다. 자바스크립트 확장판이랍니다.
* @babel/preset-env : es6 -> es5이하로 설정에 맞게 트랜스파일됩니다.
* babel-loader : 자바스크립트 파일을 babel preset/plugin과 webpack을 사용하여 es5로 컴파일 해주는 plugin
  * jsx -> javascript 로 컴파일
  * html webpack plugin
* @babel/plugin-proposal-class-properties : class property 관련된 문제를 해결하기 위해 사용합니다.

[레퍼런스:babel-preset-env는 무엇이고 왜 필요한가?](https://velog.io/@pop8682/%EB%B2%88%EC%97%AD-%EC%99%9C-babel-preset%EC%9D%B4-%ED%95%84%EC%9A%94%ED%95%98%EA%B3%A0-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%9C%EA%B0%80-yhk03drm7q)

### webpack 설치

```
yarn add -D webpack webpack-dev-server webpack-cli html-webpack-plugin
```

* webpack
  * 모든 리액트 파일을 하나의 컴파일된 하나의 자바스크립트 파일에 넣기 위해
* webpack-dev-server
  * live reload
* webpack-cli
  * build 스크립트를 통해 webpack 커맨드를 사용하기 위해
* html-webpack-plugin
  * 나중에 webpack.config.js에서 사용할 플러그인
* Loaders : 코드 불러옴
  * transform the source code of a module.
    * style-loader css를 dom에 추가
    * sass-loader SASS파일을 CSS로 컴파일
    * babel-loader babel로 javascript 코드를 transpile
  * do the pre-processing transformation of virtually any file format when you use sth like require("my-loader!./my-awesome-module") in your code
* Plugins :컴파일러
  * Webpack의 핵심 기능
  * Webpack은 기본적으로 대부분의 source 코드를 bundle파일로 번환한다.
  * loader가 부족하면 pluging을 사용하여 webpack의 기능을 추가한다.
* preset :plugin의 집합
  * plugin이 필요할 때마다 매번 webpack에서 설정하는 것은 귀찮은 일이므로 plugin들을 모아놓은 preset을 한번에 추가하여 관리할 수 있습니다.

### webpack.config.js

웹팩을 다루기 위한 설정파일 만듭니다. `webpack.config.js` 기본적으로 이 이름이여야합니다. 다른이름을 사용시 웹팩의 옵션을 설정해주어야 합니다.

```js
const path = require('path'); // core nodejs 모듈 중 하나, 파일 경로 설정할 때 사용
const HtmlWebpackPlugin = require('html-webpack-plugin'); // index.html 파일을 dist 폴더에 index_bundle.js 파일과 함께 자동으로 생성, 우리는 그냥 시작만 하고싶지 귀찮게 index.html 파일까지 만들고 싶지 않다.!!

module.exports = {
  // moduel export (옛날 방식..)
  entry: './src/index', // 리액트 파일이 시작하는 곳
  resolve: {
    extensions: ['.jsx', '.js'],
  },
  output: {
    // bundled compiled 파일
    path: path.join(__dirname, '/dist'), //__dirname : 현재 디렉토리, dist 폴더에 모든 컴파일된 하나의 번들파일을 넣을 예정
    filename: 'index_bundle.js',
  },
  module: {
    // javascript 모듈을 생성할 규칙을 지정 (node_module을 제외한.js 파일을 babel-loader로 불러와 모듈을 생성
    rules: [
      {
        test: /\.jsx?/, // .js, .jsx로 끝나는 babel이 컴파일하게 할 모든 파일
        exclude: /node_module/, // node module 폴더는 babel 컴파일에서 제외
        use: {
          loader: 'babel-loader', // babel loader가 파이프를 통해 js 코드를 불러옴
          options: {
            presets: ['@babel/preset-env', '@babel/preset-react'],
            plugins: ['@babel/proposal-class-properties'],
          },
        },
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html', // 생성한 템플릿 파일
    }),
  ],
};
```

* \_\_dirname : 노드 변수로 현재 모듈의 디렉토리를 리턴합니다.
* HtmlWebpackPlugin : 컴파일 이후 index.html 설정한 파일에 js, css 스크립트를 생성해줍니다.
  * template에 지정된 index.html에 모든 static 파일들을 긁어모은 index\_bundle.js 파일을 형식으로 연결해줍니다.
* resolve: 확장자를 생략할 수 있습니다.
  * entry에 들어갈 파일이나, import 할때, js, jsx 생략가능합니다.
* entry : 컴파일 할 파일
  * /src/app
* output : 컴파일 이후 파일위치
  * \_\_dirname/dist/index\_bundle.js
* module : 모듈의 컴파일 형식
  * es6 문법을 es5으로 바꾸기 위해 webpack이 js, jsx를 포함한 모든 파일을 babel을 통하여 컴파일 시킵니다.
* plugin : 사용할 plugins
  * 여기서는 htmlwebpackPlugin을 사용하여 index.html과 index\_bundle.js에 연결해줍니다.

html-webpack-plugin은 script 태그안에 넣은 webpack 번들 파일과 함께 html5 파일을 생성합니다. HTMLWebpackPlugin이 index.html의 script 태그안에 컴파일된 bundle 파일을 심어줄 것입니다. new HtmlWebpackPlugin()을 위의 예시처럼 그냥 사용하는 것도 좋지만 이렇게 템플릿을 만들어 놓으면 커스터마이징 하기 편합니다.

### index.html 결국에는 html에 webpack으로 합쳐진 js파일을 붙이는 방식입니다.

프로젝트 루트 디렉토리에서 src라는 디렉토리를 생성해서 index.html를 추가해줍니다. 이 구조는 템플릿 처럼 똑같습니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>My React App</title>
</head>
<body>
    <div id="root"></div>
</body>
</html>
```

### babelrc

* babel-preset-env와 babel-preset-react와 같이 preset을 사용하고 싶으면 root폴더에 .babelrc을 생성하여 사용하고자할 preset을 설정하면 됩니다.
* plugin들을 각각의 npm dependency를 가지고 있습니다. 하지만 설치시 매번 .bablrc에 설정을 해야하므로 그 두가지를 모두 해결해줄 preset을 사용하면됩니다. preset을 설치하고 설정하므로서 preset이 가진 plugin들을 설정할 필요없이 사용할 수 있게 됩니다.

.babelrc 를 만들지 않고 webpack.config.js에 추가해도 됩니다.

```js
  module: {
    // javascript 모듈을 생성할 규칙을 지정 (node_module을 제외한.js 파일을 babel-loader로 불러와 모듈을 생성
    rules: [
      {
        test: /\.jsx?/, // .js, .jsx로 끝나는 babel이 컴파일하게 할 모든 파일
        exclude: /node_module/, // node module 폴더는 babel 컴파일에서 제외
        use: {
          loader: 'babel-loader', // babel loader가 파이프를 통해 js 코드를 불러옴
          options: {
            presets: ['@babel/preset-env', '@babel/preset-react'],
            plugins: ['@babel/proposal-class-properties'],
          },
        },
      },
    ],
  },
```

### 마지막으로 npm run scripts

`package.json`에 npm run scripts를 추가하여 webpack을 통해 프로젝트를 빌드하고 실행해봅시다.

```
  "scripts": {
    "start":"webpack-dev-server --mode development --open --hot",                    // webpack-dev-server, --open : 자동으로 브라우저 열어줌, --hot : hot realod 저장했을 때 자동적으로 reload 해줌
    "build":"webpack --mode production"                                              // dist 폴더에 컴파일된 파일 다 넣어줌
  },
```

### 추가로 해야할 것

css 를 styled-component로 할지 sass로 할지 결정해서 추가로 css를 적용해봐야겠습니다.

참고:

[레퍼런스: React webpack 밑바닥에서 부터 설정해보기 - @pop8682](https://velog.io/@pop8682/%EB%B2%88%EC%97%AD-React-webpack-%EC%84%A4%EC%A0%95-%EC%B2%98%EC%9D%8C%EB%B6%80%ED%84%B0-%ED%95%B4%EB%B3%B4%EA%B8%B0) [레퍼런스: Babel과 Webpack을 이용한 ES6 환경 구축](https://poiemaweb.com/es6-babel-webpack-2)
