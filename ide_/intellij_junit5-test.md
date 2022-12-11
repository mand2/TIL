# gradle 말고 Junit5로 test 실행하기

test run을 시켰는데 자동적으로 gradle test가 실행되었다.\
원하는 junit5 테스트가 자동적으로 실행시키기 위한 설정방법을 찾았다

\


### 1. build.gradle.kts 설정하기

```kotlin
dependencies {
    testImplementation(platform("org.junit:junit-bom:5.8.2"))
    testImplementation("org.junit.jupiter:junit-jupiter")
    testRuntimeOnly ("org.junit.jupiter:junit-jupiter-engine:5.8.2")
}

tasks.test {
    useJUnitPlatform()
}
```

\


### 2. preferences setting - build setting

Build, Execution, Deployment 탭 에서 `build tools` - `gradle` - Gradle projects 세팅을 아래 이미지에 표시한 대로 변경해준다.

![setting 1](../images/IDE\_기타/intellij\_junit5\_test\_runner.png)

\


### 실행 결과

내가 원하는 대로 junit5 테스트가 실행되었다. `DisplayName` 설정한 대로도 나옴 😊

![setting 1](../images/IDE\_기타/intellij\_junit5\_test\_runner2.png)
