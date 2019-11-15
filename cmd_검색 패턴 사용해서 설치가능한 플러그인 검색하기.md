# 검색 패턴 사용해서 설치가능한 플러그인 검색하기

```
$ yarn list --pattern [글자] --depth 1
```

<br>

예 ) 

```
$ yarn list --pattern @babel/plugin --depth 1
yarn list v1.19.1
├─ @babel/plugin-proposal-async-generator-functions@7.2.0
├─ @babel/plugin-proposal-class-properties@7.7.0
├─ @babel/plugin-proposal-decorators@7.7.0
├─ @babel/plugin-proposal-dynamic-import@7.5.0
├─ @babel/plugin-proposal-json-strings@7.2.0
├─ @babel/plugin-proposal-object-rest-spread@7.6.2
├─ @babel/plugin-proposal-optional-catch-binding@7.2.0
├─ @babel/plugin-proposal-unicode-property-regex@7.6.2
├─ @babel/plugin-syntax-async-generators@7.2.0
├─ @babel/plugin-syntax-decorators@7.2.0
├─ @babel/plugin-syntax-dynamic-import@7.2.0
.......
├─ @babel/traverse@7.6.2
├─ @babel/types@7.6.1
├─ @jest/transform@24.9.0
│  └─ @babel/core@7.6.2
├─ @svgr/plugin-jsx@4.3.3
│  └─ @babel/core@7.6.2
..........
```

내가 설치한 부분이 조금 더 밝은 색, 아닌 부분은 회색으로 나타나서 리스트상에서 설치 여부를 명확하게 볼 수 있다.