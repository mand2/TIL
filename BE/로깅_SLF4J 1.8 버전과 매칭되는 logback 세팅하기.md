# SLF4J 1.8 버전 + logback NOP 에러 해결하기


slf4j 2.xx 최신 버전이 나와서 세팅하다가,  
**SLF4J: Defaulting to no-operation (NOP) logger implementation** 라는 에러가 나왔다.

전제
- kotlin
- gradle
- intellij
- jdk 11 (amazon-corretto)

<br>

처음에 세팅한 버전

```kotlin
// build.gradle.kts
dependencies {
    testImplementation(kotlin("test"))
    implementation("org.slf4j:slf4j-api:2.0.0-alpha6")
    implementation("ch.qos.logback:logback-classic:1.2.10")

    testImplementation(platform("org.junit:junit-bom:5.8.2"))
    testImplementation("org.junit.jupiter:junit-jupiter")
}
```

<br>
<br>

아니 난 완벽하게 했다고 생각했는데 왜 안되지?!?! 하면서 slf4j [문서](https://www.slf4j.org/faq.html#changesInVersion18) 를 봤다.

### 문서 내용
**What are SLF4J's requirements?**

![setting 1](/images/BE/SLF4J1.8_logback_setting(1).png)

<br>

### 생각의 흐름
문서를 읽고, '나는 logback-classic이니까 jdk버전이 맞나 찾아보면 되네'   
→ 처음부터 jdk 11 으로 했으니 괜찮은데?  
→ 심지어 1.2.10은 2021년 12월, 즉 한달전에 릴리즈 된 버전인데?
→ 뭐야 다 맞는 거 같은데 왜 안되는거야..!! 🤬🤬🤬

→ 바로 아래에 2.0.0 버전부터 바뀐 특징이 있네? 이거 봐야겠다.

**What has changed in SLF4J version 2.0.0?**

There are no client facing API changes in 2.0.x. For most users upgrading to version 2.0..x should be a drop-in replacement as long as the logging provider is updated as well.  
In version 2.0.0, SLF4J has been modularized per [JPMS/Jigsaw](http://openjdk.java.net/projects/jigsaw/spec/) specification. The [JPMS module names](https://www.slf4j.org/faq.html#jmpsModuleNames) are listed in another FAQ entry.  
More visibly, slf4j-api now relies on the [ServiceLoader](https://docs.oracle.com/javase/8/docs/api/java/util/ServiceLoader.html) mechanism to find its logging backend. SLF4J 1.7.x and earlier versions relied on the static binder mechanism which is no longer honored by slf4j-api version 2.0.x. More specifically, when initializing the `LoggerFactory`class will no longer search for the `StaticLoggerBinder`class on the class path.  
Instead of "bindings" now `org.slf4j.LoggerFactory` searches for "providers". These ship for example with*slf4j-nop-2.0.x.jar*,*slf4j-simple-2.0.x.jar*or*slf4j-jdk14-2.0.x.jar*.  
The following table describes the results when various slf4j-api and slf4j-simple versions are placed on the class path. Please note that the table below applies by analogy not just to slf4j-simple but also to other providers such as slf4j-reload4j, logback-classic, slf4j-jdk14, etc...  

![setting 2](/images/BE/SLF4J1.8_logback_setting(2).png)

<br>

### SLF4J 1.8 버전 특징 정리
정리하자면,  
`바인딩` 모듈 대신에 `providers` 라는 모듈로 대체했다   
→ 바인더 버전을 slf4j-api 버전과 같은 버전으로(2.0.x 대 버전)하면 된다고 했는데, 
대표적으로 slf4j-simple을 예로 들었기 때문에 
난 ‘음 logback-classic 1.2.xx 버전이 2.0.x 버전이랑 매칭되는 거겠지’ 라고 안일하게 생각했고, 
**3시간 삽질**하는 데 결정타가 되었다.. ㅋㅋ

짧게 내 3시간 삽질 기록을 하자면:    
> logback.xml 설정을 잘못했나? 싶어서 로그백 설정하는 방법을 다시 보기도 하고,   
> 쓸데없이 application.yml 세팅도 또 해야되나..   
> vm option 으로 주면 되나.. 했는데   
> ~~상관없는 삽질~~이었고 그저 logback-classic 버전만 맞춰주면 되는 거였다

<br>
<br>

## 결론 
결론은 logback 바인더로 설정하려면 `1.3.xx 버전`으로 바꾸면 된다.

그래서 잘 처리된 상태로는 아래와 같다.

```kotlin
// build.gradle.kts
dependencies {
    testImplementation(kotlin("test"))
    implementation("org.slf4j:slf4j-api:2.0.0-alpha6")
    implementation("ch.qos.logback:logback-classic:1.3.0-alpha12")
    implementation("org.slf4j:log4j-over-slf4j:2.0.0-alpha6")

    testImplementation(platform("org.junit:junit-bom:5.8.2"))
    testImplementation("org.junit.jupiter:junit-jupiter")
}
```

<br>

## TIL: SLF4J 바인더 종류
오늘 말한 logback 은 SLF4J 의 바인더 종류 중 하나다 (이젠 `provider` 라고 불러야 하지만 바인더로 퉁치기)

- slf4j-log4j12-{version}.jar : log4j 버전 1.2에 대한 바인딩
- slf4j-jdk14-{version}.jar : java.util.logging(JDK1.4 로깅)에 대한 바인딩
- slf4j-nop-{version}.jar : NOP에 대한 바인딩. 모든 로깅을 삭제 (=로그가 찍히지 않음)
- slf4j-simple-{version}.jar : 모든 이벤트를 System.err에 출력하는 단순 구현에 바인딩 (INFO 레벨 이상만 가능. 즉 DEBUG 이하 레벨은 로그 안찍힘)
- slf4j-jcl-{version}.jar : JCL(Jakarata Commons Logging)에 대한 바인딩. 모든 SLF4J 로깅을 JCL에 위임
- **logback-classic-{version}.jar**: **SLF4J 인터페이스를 직접 구현한 것.**
  참고로 ch.qos.logback.classic.Logger 클래스는 SLF4J의 org.slf4j.Logger인터페이스를 직접 구현한 클래스다.

---

**참고**: 로그 구현체 + SLF4J 프레임워크 관련 잘 정리되어 있는 블로그 (많은 도움이 되었다 👀)

- [https://gmlwjd9405.github.io/2019/01/04/logging-with-slf4j.html](https://gmlwjd9405.github.io/2019/01/04/logging-with-slf4j.html)
- [https://dailylifecoding.tistory.com/entry/Java-로그Log을-위한-SLF4J-Logback](https://dailylifecoding.tistory.com/entry/Java-%EB%A1%9C%EA%B7%B8Log%EC%9D%84-%EC%9C%84%ED%95%9C-SLF4J-Logback)
