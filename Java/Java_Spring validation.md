# validation

중요하다고 생각했던 포스팅에는 ✨ 로 표시.

## 1. NPE 를 피하는 방법

- ✨ Avoid Check for Null Statement in Java [Baeldung](https://www.baeldung.com/java-avoid-null-check)

## 2. Guava

Guava pom.xml 최신버전 2020.07.07 기준 

```xml
<!-- https://mvnrepository.com/artifact/com.google.guava/guava -->
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>29.0-jre</version>
</dependency>
```

- Guide to Guava's PreConditions [Baeldung](https://www.baeldung.com/guava-preconditions)
- ✨ [Geeks for Geeks](https://www.geeksforgeeks.org/preconditions-guava-java/)
- Custom exception with Guava Preconditions [stackoverflow](https://stackoverflow.com/questions/20707056/custom-exception-with-guava-preconditions)

## 3. 그 외-precondition 사용법

COMPARISON OF WAYS TO CHECK PRECONDITIONS IN JAVA [URL](https://www.sw-engineering-candies.com/blog-1/comparison-of-ways-to-check-preconditions-in-java) 

## 4. ExceptionHandler + ControllerAdvice

- [Spring Boot #18] 스프링 부트 ExceptionHandler [새로비](https://engkimbs.tistory.com/779)
- ✨ @ControllerAdvice, @ExceptionHandler를 이용한 예외처리 분리, 통합하기 [정아마추어코딩블로그](https://jeong-pro.tistory.com/195)
- ✨ Validation and Exception Handling with Spring [medium](https://medium.com/sprang/validation-and-exception-handling-with-spring-ba44b3ee0723)
- java Exception 처리 어떻게 할까 [BlueSky_블로그](https://luvstudy.tistory.com/50)
- ✨ Spring Guide - Exception 전략 [Yun 블로그](https://cheese10yun.github.io/spring-guide-exception/)