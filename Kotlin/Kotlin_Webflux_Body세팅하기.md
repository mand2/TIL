# Spring webflux - Functional Endpoints 정리
신규 프로젝트 중 kotlin + webflux 를 사용하는 프로젝트가 있다.  
이 프로젝트는 `@RequestMapping` 과 같은 어노테이션 기반의 API 가 아니라, Functional Endpoints 로 사용하는데
이 Functional Endpoints를 한국어로 뭐라 해야할지.. 지금 공부중이라 일단 TIL 파트에 올려놓고 정리함!


## webflux 간단 정리

Spring Webflux 는 두 가지 타입의 프로그래밍 모델을 지원한다.
1. 어노테이션 기반 리액티브 컴포넌트: 기존의 Spring MVC. `spring-web` 모듈에서 제공하는 어노테이션을 사용.(예- @Controller)
2. 함수형 엔드포인트 : 람다기반, jdk 8, 경량화, 함수형프로그래밍 모델. 코틀린의 경우 `Coroutines`와 같이 사용하는 걸 추천함. 

router = api 엔드포인트(api path + method 인입)  
handler = @controller 라고하는데, 왠지 나는 서비스 단 느낌도 난다. (이유: 비즈니스 로직이 다 이 핸들러에 있음)  

Http Request는 `HandlerFunction` 으로 핸들링하며, 
이 인입된 요청은 `RouterFunction` 으로 라우팅 된다(api endpoint matching).  


### HTTP 요청 처리 흐름 (ServerSide)
1. Router 에서 method, uri 모두 매칭되는 RouterFunction이 있는지 확인
2. 있으면 해당 handler 함수 실행한다.


- HandlerFunction = `@RequestMapping` 메서드의 Body에 해당한다. (GET에도 body가 들어갈 수 있다 by. 김영한님 http 네트워크 수업)
- RouterFunction = `@RequestMapping` 에 행동(method를 얘기하는 걸까?)까지 추가됨 (어노테이션기반에선 data 만 주고받음.)
router function builder 로 router 설정을 하는데,  
예) `GET`메서드: 아래와 같이 `GET(String=uri, HandlerFunction)`형식으로 세팅을 할 수 있다.  
```kotlin
val route = coRouter {
    GET("/hello-world", accept(TEXT_PLAIN)) { // HandlerFunction 부분. 함수라 {}로 표현.
        ServerResponse.ok().bodyValueAndAwait("Hello World")
    }
}
```

> `RouterFunction` ?  
> `ServerRequest`을 request 로 가지고, 
> response 로 지연된 `ServerResponse`을 반환.  
> (`자바`: `Mono<ServerResponse>` 를, `코틀린`: suspend fun 함수로 ServerResponse 를 반환)


## ServerRequest

HTTP method, URI, headers, and query parameters, body 에 접근 할 수 있다. body는 `body` 메서드를 사용해야 함.

### body 접근 방법
1. mono + content_type{text/plain or x-www-form-urlencoded}
```kotlin
val string = request.body(BodyExtractors.toMono(String::class.java)).awaitSingle()
val string = request.awaitBody<String>() // 간단버전
```
2. flux / mono + content_type{json}
```kotlin
val people = request.body(BodyExtractors.toFlux(Person::class.java)).asFlow()
val people = request.bodyToFlow<Person>() // 간단버전
val people = request.body(BodyExtractors.toMono(Person::class.java)).awaitFirst()
```
flux 에선 JSON / XML과 같은 직렬화된 폼을 request 로 받아야 한다.
mono 의 간단버전은 잘 모르겠음.. awaitBody로 되는지 테스트 필요.

3. form data
```kotlin
val map = request.awaitFormData()
```
4. multipart data as a map
```kotlin
val map = request.awaitMultipartData()
```
5. multiparts 한번에 스트리밍 형식으로 받아올 때
```kotlin
val parts = request.body(BodyExtractors.toParts()).asFlow()
```

