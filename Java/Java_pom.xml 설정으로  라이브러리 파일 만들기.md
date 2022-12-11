# pom.xml build 설정하기

스펙

* intellij
* JDK 1.8
* maven 4.0.0

\


작성한 코드를 라이브러리 형식으로 만들기 위해 `.jar` 파일로 만들었다. 내가 세팅한 버전 등에 맞게 내보내기 위해 + 설정한 dependency 까지 보내주기 위하여 설정하였다.

\


그전에 앞서, apache maven project 사이트에서 말하는 최소 사양은 다음과 같다.

* Maven 3.0 이상
* JDK 1.7 이상

\
\
\


### Q. pom.xml에 사용한 dependency를 어떻게 내보내야 하는가?

두 가지 버전이 있다.

> 1. maven-assembly-plugin 사용
> 2. maven-jar-plugin + maven-dependency-plugin 함께 사용

\


1 은 내부에 전 dependency를 저장하여 보내주기 때문에 용량이 크다.

2 는 manifest.mf 파일에 라이브러리의 상대경로를 저장해서 불러오도록 한다.

> manifest 파일이란 ? [바로가기](https://www.baeldung.com/java-jar-manifest)
>
> pom 파일에 대해 더 설명을 원할 때 [바로가기](https://maven.apache.org/pom.html)

\


용량문제 + 1의 경우 **conflict 문제가 있다**\*는 말이 있어 2를 선택함. [출처](https://stackoverflow.com/questions/38548271/difference-between-maven-plugins-assembly-plugins-jar-plugins-shaded-plugi/38558129)

실제로 2로 할 때의 용량이 26KB 인데 반해 1의 경우 4600KB 정도로 용량차이가 큼.

\
\


**버전 1**

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-assembly-plugin</artifactId>
            <version>3.2.0</version>
            <configuration>
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
            </configuration>
            <executions>
                <execution>
                    <id>make-assembly</id> <!-- this is used for inheritance merges -->
                    <phase>package</phase> <!-- bind to the packaging phase -->
                    <goals>
                        <goal>single</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>    
```

\


**버전 2**

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <version>3.2.0</version>
            <configuration>
                <archive>
                    <manifest>
                        <addClasspath>true</addClasspath>
                        <packageName>com.demo.sample</packageName>
                        <classpathPrefix>lib/</classpathPrefix>
                    </manifest>
                </archive>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-dependency-plugin</artifactId>
            <executions>
                <execution>
                    <id>copy-dependencies</id>
                    <phase>package</phase>
                    <goals>
                        <goal>copy-dependencies</goal>
                    </goals>
                    <configuration>
                        <outputDirectory>${project.build.directory}/lib</outputDirectory>
                        <overWriteReleases>false</overWriteReleases>
                        <overWriteSnapshots>false</overWriteSnapshots>
                        <overWriteIfNewer>true</overWriteIfNewer>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

필요한 부분들

* packageName
* mainClass : `main()` 가 있으면 포함해야 함.
* classpathPrefix

\
\
\


### Q. java version setting은?

버전 세팅에도 두가지 방법이 있다.

\


**1. build 할 때 세팅하는 방법**

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.1</version>
    <configuration>
        <source>${java.version}</source>
        <target>${java.version}</target>
        <encoding>utf-8</encoding>
    </configuration>
</plugin>
```

\


**2. 처음부터 세팅**

```xml
<properties>
    <java.version>1.8</java.version>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
</properties>
```

\
\
\


### Q. jar 파일 이름 설정가능한지?

가능하다. 아래와 같이 pom.xml 에서 설정가능.

```xml
<groupId>com.demo</groupId>
<artifactId>demo-sample</artifactId>
<version>1.0.0</version>
<name>sample</name>
<description>DEMO Java Sample project for Spring Boot</description>
[...]
<build>
	<finalName>${project.artifactId}-${project.version}</finalName>
</build>
```

* 라이브러리 명( == jar 파일명) `<finalName>` 에 정해진 대로 : demo-sample-1.0.0.jar
* 라이브러리 내부의 패키지명(class 파일을 갖고있는 패키지임) `maven-jar-plugin` 의 `<packageName>` 을 따름 : com.demo.sample
* 라이브러리 내부의 META-INF maven.{groupId}.{artifactId} 로 나옴

\
\
\


### 마지막

intellij 기준으로 maven - lifecycle - clean 후 package 로 하면 `target` 폴더에 원하는 `.jar` 가 만들어진다. 이 파일은 라이브러리형식으로 되었기 때문에 다른 프로젝트에서 쓸 수 있다-!

\
\
\
\
\
\


### 번외 : Apache Maven Archiver

자세한 설명보러가기>>> [docs](http://maven.apache.org/shared/maven-archiver/index.html)

```xml
<archive>
  <addMavenDescriptor/>
  <compress/>
  <forced/>
  <index/>
  <pomPropertiesFile/>
 
  <manifestFile/>
  <manifest>
    <addClasspath/>
    <addDefaultEntries/>
    <addDefaultImplementationEntries/>
    <addDefaultSpecificationEntries/>
    <addBuildEnvironmentEntries/>
    <addExtensions/>
    <classpathLayoutType/>
    <classpathPrefix/>
    <customClasspathLayout/>
    <mainClass/>
    <packageName/>
    <useUniqueVersions/>
  </manifest>
  <manifestEntries>
    <key>value</key>
  </manifestEntries>
  <manifestSections>
    <manifestSection>
      <name/>
      <manifestEntries>
        <key>value</key>
      </manifestEntries>
    <manifestSection/>
  </manifestSections>
</archive>
```

\
\
\


***

\


#### 더 자세한 내용은

https://maven.apache.org/plugins/maven-jar-plugin/
