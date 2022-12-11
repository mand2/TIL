# intellij cannot resolve symbol QueryDslRepositorySupport



### 1 Lifecycle 실행

queryDsl Qclass symbol error 발생 시 : 

AXBoot Initializer로 생성한 프로젝트를 IntelliJ IDEA에서 열고 실행하려고 하면, QMenu, QCommonCode 등의 JPA 도메인 클래스 관련 오류가 발생하면서 빌드가 실패한다. 

이럴 때는 "View > Tools Windows > Maven Projects" 메뉴를 이용해서 Maven 패널을 열고, Lifecycle 하위의 clean, package 골을 차례로 실행한다. 



이 오류는 Querydsl 관련 클래스가 없어서 발생하는데, 위의 과정을 통해서 생성하는 것이다.

--------------

### 2 Project structure에서 Module 추가

1. [File] -> [Project structure] -> [Module] -> [Sources] 탭으로 이동

2. (오른쪽 창에 있는) QueryDSL 소스디렉토리 선택

![img](https://t1.daumcdn.net/cfile/tistory/241CB350520AD22630)

3. 마우스 우클릭

4. [Source] 선택
    Source로 선택된 디렉토리는 파란색으로 변경되고, 왼쪽에 등록된다. 

![img](https://t1.daumcdn.net/cfile/tistory/27303250520AD22730)



https://java.ihoney.pe.kr/279

