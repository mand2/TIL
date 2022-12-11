# pub/sub 구조

**목적**

1. 비동기 통신 모델(?)인 pub/sub구조 알기
2. 그 구조를 따른 redis , Apache Kafka 공부하기
3. gRPC 란?



참고할 블로그/문서 아래로 갈수록 길다.

### pub/sub

- [Pub/Sub model 간단 정리](https://sugerent.tistory.com/585)
- [pub/sub 구조란](https://2kindsofcs.tistory.com/6#:~:text=%EB%B0%9C%ED%96%89%2D%EA%B5%AC%EB%8F%85%20%EB%AA%A8%EB%8D%B8%EC%9D%80%20%EB%B9%84%EB%8F%99%EA%B8%B0,%ED%95%9C%20%EC%88%98%EC%8B%A0%EC%9E%90%EC%97%90%EA%B2%8C%20%EC%A0%84%EB%8B%AC%EB%90%9C%EB%8B%A4.)
- [구글 cloud pub/sub 문서](https://cloud.google.com/pubsub/docs/overview?hl=ko)
- [번역-FE 초보를 위한 Pub-Sub 패턴](https://rinae.dev/posts/why-every-beginner-front-end-developer-should-know-publish-subscribe-pattern-kr)



### pub/sub을 이용한 아키텍쳐 관련 기술블로그

- 우아한형제들기술블로그 [실시간 서비스 경험기(배달운영시스템)](https://woowabros.github.io/woowabros/2017/09/12/realtime-service.html)
- VCNC 기술블로그 [타다 시스템 아키텍처](http://engineering.vcnc.co.kr/2019/01/tada-system-architecture/)
- 라인기술블로그 [LINE LIVE 채팅 기능의 기반이 되는 아키텍처](https://engineering.linecorp.com/ko/blog/the-architecture-behind-chatting-on-line-live/)



### redis

- [우아한 Redis 세미나 후기](https://ict-nroo.tistory.com/133)



### kafka

- 우아한형제들기술블로그 [Kafka를 이용한 작업 큐 라이브러리 ‘Decaton’ 활용 사례](https://engineering.linecorp.com/ko/blog/decaton-case-studies/)