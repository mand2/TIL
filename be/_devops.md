# 개발상식: DevOps

##### 출처 URL @pa324 [바로가기](https://velog.io/@pa324/%EA%B0%9C%EB%B0%9C%EC%83%81%EC%8B%9D-%EB%8D%B0%EB%B8%8C%EC%98%B5%EC%8A%A4DevOps)

어딜 가든 데브옵스 데브옵스 이러는데 데브옵스가 무엇인지 간단하게 정리해봤다.



## 데브옵스란?

데브옵스는 개발과 운영을 합친 단어이다. 서비스의 패치를 위해서 몇 달간의 작업 후 배포하던 고전적인 방식과 달리, 현재는 빈번한 서비스 배포가 주류를 이루고 있다. 대부분의 서비스가 설치 기반에서 웹 기반으로 바뀌었으면 마이크로 서비스와 애자일 개발 방법론에 대한 관심이 커져서, 빈번한 서비스 배포가 필요해졌다.

## 데브옵스 이점

- 속도
  - 배포까지의 빠른 작업속도를 효율적으로 제공하기 때문에 시장 변화에 빠르게 대처
- 빠른배포
  - 새로운 릴리즈와 버그픽스를 빠르게 배포할 수 있으며, 그로 인해 고객의 요구에 빠르게 대응 가능하다.
- 협업
  - 개발팀,운영팀이 긴밀하게 협업할 수 있기 때무넹 책임을 공유하고 업무를 결합해서 비효율을 줄이고 시간절약

## SCM

팀 단위로 개발되는 소스를 지속적으로 관리해야한다.

## CI/CD

하나의 서비스를 여러명의 팀 단위로 작업을 한다. 각각 개발한 코드 빌드부터 배포까지 걸리는 시간 단축을 위해서 CI/CD 구축이 필요

### CI(Continous Integration)

Build와 test를 실시하는 절차이다. 일반적으로 젠킨스를 많이 활용함

### CD (Continous Deployment)

빌드단계 이후의 소프트웨어의 배포를 자동화한다. 개발자가 원할 때 언제나 즉시 테스트 혹은 프로덕션에 지속적으로 배포할 수 있다.