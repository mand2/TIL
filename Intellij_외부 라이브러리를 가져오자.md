# 외부 라이브러리를 pom.xml 에 가져오자

maven이나 gradle에 **정식으로 올라가지 않은** 개인/회사 내부용 모듈을 추가해야할 때 사용하는 방법. 배포 종속성까지 생각하는 pom.xml 만들기 🔥



### 1 외부용 lib 파일 추가

프로젝트 디렉토리에서 `\lib` 폴더 만들고 아래에 원하는 `.jar` 파일들을 넣는다.

> mand-api 프로젝트
>
>  - lib
>     - ny-demo-api.jar
>       	- groupId: com.ny.demo
>        	 	  	- artifactId: api
> 	   	 	  	- version : 1.0.2
> - output
> - src\main\java..
> - src\test...
> - target



### 2 의존성 추가

pom.xml에 dependency 추가한다. 예를들어 `ny-demo-api.jar`를 모듈로 추가해야 한다면, 

```xml
<dependency>
    <groupId>com.ny.demo</groupId>
    <artifactId>api</artifactId>
    <version>1.0.2</version>
</dependency>
```



### 3 플러그인 추가

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
    </configuration>
</plugin>
```

내 프로젝트의 java.version과, 외부 라이브러리에서 설정한 java.version 모두 1.8로 맞아야 함



### 4 Lifecycle-compile

외부 라이브러리에 있는 클래스를 import 할 때 필요. compiler 플러그인으로 인해 `lifecycle-clean` 을 하면 classnotfoundexception 인가.. 예외 처리 발생되며, `compile` 로 실행 후 class import 가능.

> 📌 dependencies에 잘 연결되었는지 확인
>
> 📌 `Project Settings-Modules`에서도 확인 하기




### 5 배포시 종속성 연결
1. `Project Settings-Artifacts` 에서 ➕ 버튼 클릭
2. `JAR-From module with dependencies...` 클릭
3. 정확한 Main Class 선택 후 OK
4. Output directory를 따로 설정하려면 찾는다.
   `# 1 ` 의 프로젝트 디렉토리에 설정한 대로 `\output` 폴더로 변경함.
5. 변경 사항을 저장한다.
6. 배포시 `Build` - `Build Artifacts...` - `[설정한 jar 파일]` - `Build` 클릭하면 원하는 output 폴더에 jar가 종속성을 가진 채로 생성됨 👏👏👏



### 6 deprecated 된 방법

```xml
<dependency>
    <groupId>some.group.id</groupId>
    <artifactId>some.artifat.id</artifactId>
    <version>some.version.no</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/libs/yourActualJarFileName.jar</systemPath>
</dependency>
```

이런 식으로 하면 내부적으로 혼자 사용할 땐 괜찮지만, **배포할 땐 맞지 않는다는 강렬한 경고창**💥이 뜬다. 심지어 미래에는 pom.xml에서 사용하지 못하게 할거라고 경고창이 뜸 😥

이유는 scope를 system으로 설정하고 systemPath로 하드코딩했기 때문..







