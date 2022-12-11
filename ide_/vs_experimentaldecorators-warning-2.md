# vs\_experimentalDecorators warning 이슈 해결방법2

#### VS Code에서 experimentalDecorators warning 이슈 해결방법2

어제 mobx 설치하고 완성까지 했는데 decorator로 하기 시작하니까 또 에러ㅓㅓㅓ ㅎ,,,,,,,, tsconfig.json으로 만들고 구동하려니까 계속 typescript 설치하라고 뜬다.. 나는 typescript 만들기 싫은데?! 라고 생각해서 찾아보니 두 가지 방법을 찾음ㅋ

\
\


#### 1

실제 settings 로 가서 검색을 한 후 아래의 체크박스를 enable로 설정해주면 된다.

\
!\[1573780012512]\(C:\Users\Nayeon\AppData\Roaming\Typora\typora-user-images\1573780012512.png)\
\


#### 2

다 설정해주는 게 싫다! 하면 실제 vscode의 settings.json로 들어가서 decorators만 오류 나지 않도록 하는 걸로 해도 됨.

path : `\AppData\Roaming\Code\User\settings.json`

> “javascript.implicitProjectConfig.experimentalDecorators”: true

추가만 해주면 된다.
