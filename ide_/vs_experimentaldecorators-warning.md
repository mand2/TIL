# vs\_experimentalDecorators warning 이슈 해결방법

#### VS Code에서 experimentalDecorators warning 이슈 해결방법

mobx로 프로젝트 만든 걸 보려고 하는데 아래 사진과 같이 계속 에러가 나왔다.

**이슈 사진**

![VSCode and experimentalDecorators warning](https://ihatetomatoes.net/wp-content/uploads/2017/01/img\_vscode-experimentalDecorators-warning.png)

클래스 App에 빨간 줄이 쳐져있다. error 발생!! 데코레이터를 인식을 못하는 것 같은데,,, ? 라고 생각했다.

처음엔 mobx이 설정이 제대로 안된건줄알고 다시 설치했는데 아님,,

> ```
> $ yarn add mobx mobx-react
> $ yarn add @babel/plugin-proposal-class-properties @babel/plugin-proposal-decorators
> ```

알고보니 그냥 VS code 자체의 에러임ㅎㅎ...

\
\
\


#### 해결방법

`tsconfig.json` 파일을 루트 디렉토리에 만들고 아래의 내용을 쓴다

```json
{
    "compilerOptions": {
        "experimentalDecorators": true,
        "allowJs": true
    }
}
```

그 후 **VS Code를 다시 재부팅**하면 에러로 인식하지 않고 잘 된다!

\
\


***

**출처** [**URL**](https://ihatetomatoes.net/how-to-remove-experimentaldecorators-warning-in-vscode/)
