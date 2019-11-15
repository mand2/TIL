# mock or mocking 



### 1 Mock Object란 ?

###### 출처 [URL](https://medium.com/@SlackBeck/mock-object%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-85159754b2ac)

다른 누군가로부터 휴대 전화 서비스(CellphoneService) 기능을 제공 받아 이를 사용한 휴대 전화 문자 발신기(CellphoneMmsSender)를 프로그래밍 한다고 생각해 보자.

![img](https://miro.medium.com/max/60/1*l1U7ejkPRK_UBEyCS4Idow.jpeg?q=20)

![img](https://miro.medium.com/max/479/1*l1U7ejkPRK_UBEyCS4Idow.jpeg)

이를 간단하게 코드로 나타내면 아래와 같다.

![img](https://miro.medium.com/max/60/1*haWJWaeTceXjxl6tlFM5Rg.png?q=20)

![img](https://miro.medium.com/max/938/1*haWJWaeTceXjxl6tlFM5Rg.png)

CellphoneMmsSender의 send() 메소드에 대한 테스트 코드를 작성 하려면 어떻게 해야 할까? 먼저 반환값을 검증하는 것을 고려할 수 있을 것이다. 하지만 테스트 대상인 send() 메소드의 반환 타입은 void 이다. 반환 값이 아니라면 무엇을 검증해야 할까?

CellphoneMmsSender 테스트 관점에서 중요한 것은 실제 문자 메시지를 보내는 것이 아니다. 실제 문자 메시지를 보내는 것은 CellphoneService의 책임이다. 그렇다면 **CellphoneMmsSender send() 메소드에서 검증해야 하는 것은 전달 받은 메시지(msg)를 CellphoneService sendMMS()의 파라메터로 호출 했는지 여부이다.**

CellphoneService의 sendMMS 호출 여부를 테스트 하기 위해서는 CellphoneMmsSender가 참조하고 있는 CellphoneService 객체를 가짜(대역) 객체로 대체하고 이를 검증하는 방법이 있다. 여기서의 사용하는 가짜 객체를 Mock Object 라고 한다.

<br>

### Mock Object

Mock Object는 [테스트 더블(Test Double)](https://medium.com/@SlackBeck/단위-테스트-케이스와-테스트-더블-test-double-2b88cccd6a96)중 하나 이며 위키피디아에서는 아래와 같이 정의 한다.

> In [object-oriented programming](https://en.wikipedia.org/wiki/Object-oriented_programming), **mock objects** are simulated objects that mimic the behavior of real objects in controlled ways. **A programmer typically creates a mock object to test the behavior of some other object**, in much the same way that a car designer uses a [crash test dummy](https://en.wikipedia.org/wiki/Crash_test_dummy) to [simulate](https://en.wikipedia.org/wiki/Simulation) the dynamic behavior of a human in vehicle impacts. — https://en.wikipedia.org/wiki/Mock_object

‘행위를 테스트 한다(*test the behavior of some other object*)’는 것은 무슨 의미일까? 위의 휴대 전화 문자 발신기 예제에서 ‘*전달 받은 메시지(msg)를 CellphoneService sendMMS()의 파라메터로 호출 했는지 여부’*가 행위에 해당하며 이를 테스트하는 것이다. 이를 **행위 검증(Behavior Verification)**이라고 한다.

<br>

### Mock Object를 사용한 테스트 코드

직접 Mock Object를 만들어 테스트 코드를 작성해 보자. CellPhoneServiceMock은 CellPhoneService를 상속 받는다. 왜냐하면 실제 CellphoneMmsSender 객체에서 참조할 때 같은 유형의 객체이어야 동작하기 때문이다. sendMMS() 호출을 검증 하기 위한 추가적인 코드(isSendMMSCalled, getSendMsg)를 작성 하였다.

![img](https://miro.medium.com/max/664/1*msHB8w3eCgR0iKz18qWi0Q.png)

만들어진 Mock Object를 사용한 테스트 코드([JUnit](http://junit.org/junit4/) 테스트 케이스)는 아래와 같다.

![img](https://miro.medium.com/max/597/1*g1mfRsXrp--bQVPdAzhO4w.png)

CellPhoneService 대신 Mock Object를 CellphoneMmsSender의 생성자로 주입(Injection) 하였으며, CellphoneMmsSender send() 수행 시 CellPhoneServiceMock은 호출 여부를 저장 한다. 마지막으로 [JUnit Assert](http://junit.sourceforge.net/javadoc/org/junit/Assert.html) 를 사용하여 호출 여부와 예상한 메시지인지 검증 한다.

<br>

### [Mockito](http://site.mockito.org/)를 사용한 테스트 코드

Mock Object를 직접 만들어 테스트 코드를 작성할 수도 있지만, 일일이 클래스는 만들는 것은 번거럽기도 하지만 Mock 클래스 관리도 부담스럽다. 이를 지원하는 라이브러리나 프레임워크를 사용하는 것이 보다 편하고 상대적으로 시간도 절약할 수 있다. Java 진영에서 인기 있는 [Mockito](http://site.mockito.org/)를 사용하여 테스트 코드를 작성 하였다.

![img](https://miro.medium.com/max/60/1*avgIweqfuAj-bodSg-A1rg.png?q=20)

![img](https://miro.medium.com/max/541/1*avgIweqfuAj-bodSg-A1rg.png)

[Mockito](http://site.mockito.org/)에서 지원하는 mock을 사용하여 별도의 클래스를 만들지 않고 Mock Object를 만들었다. 이렇게 만들어진 Mock Object는 [Mockito](http://site.mockito.org/)의 verify 를 통해 호출 여부를 검증 한다.

<br>

### Stub과 Mock Object 무엇이 다른가?

> There is a difference in that the stub uses state verification while the mock uses behavior verification.   
> 출처 : http://martinfowler.com/articles/mocksArentStubs.html

마틴 파울러는 위의 글에서 Mock Object는 행위 검증(behavior verification)에 사용하고, Stub은 상태 검증(state verification)에 사용하는 것이라고 말하고 있다. 행위 검증은 이 글로 충분히 다루었으니, 상태 검증을 마틴 파울러 글의 메일 서비스 예시 코드를 살펴 보자.

```java
public interface MailService {
    public void send (Message msg);
}
public class MailServiceStub implements MailService {
    private List<Message> messages = new ArrayList<Message>();
    public void send (Message msg) {
         messages.add(msg);
    }
    public int numberSent() { 
         return messages.size();
    } 
}

class OrderStateTester...
public void testOrderSendsMailIfUnfilled() {
   Order order = new Order(TALISKER, 51);
   MailServiceStub mailer = new MailServiceStub();
   order.setMailer(mailer);
   order.fill(warehouse);
   assertEquals(1, mailer.numberSent());
}
```

위의 코드에서 상태(State)는 MailServiceStub의 messages 이다. 따라서 OrderStateTester은 MailServiceStub을 이용하여 상태(mailer.numberSent() — 메일 전송 횟수)를 검증(assertEquals) 한다. 결국 행위에 의해 만들어진 상태를 검증 하는 것이지 행위 자체를 검증 하고 있는 것은 아니다.

------

관련글 : [테스트 스텁(Test Stub)이란 무엇인가?](https://medium.com/@SlackBeck/테스트-스텁-test-stub-이란-무엇인가-ff9c8840c1b0)

<br><br><br><br>

### 2 단위 테스트는 왜 해야할까 

###### 출처 [URL](https://feel5ny.github.io/2017/12/08/TDD_01/)

------

목차

1. 단위테스트. 다들 중요하다고 하는데 왜 중요할까?
2. 결국 클린코드를 위한 길
3. TDD : Test Driven Development
4. 실습

------

<br>





## 1. 단위테스트. 다들 중요하다고 하는데 왜 중요할까? [+](https://cjh5414.github.io/why-pytest/)

단위테스트는 모듈이나 어플리케이션 안에 있는 개별적인 코드 단위가 예상대로 작동하는지 확인하는 반복적인 행위이다.

프로젝트에 단위 테스트를 적용하는 데에는 “내 코드가 제대로 동작하는지 확인하는 것”이라는 명백한 이유 외에도 몇 가지 장점이 있다.

- 단위 테스트는 코드가 “어떻게!”작성하는지 생각하는데 도움을 준다.
- 게다가 “무엇”을 해야하는지에 있어서 구현 선택을 검토하는데 해가 되지 않고, 그 선택들이 적절한지 아닌지 알아낸다. 주된 효과로는 단위 테스트를 추가하는 것은 애플리케이션의 유닛(함수/메소드)를 더 `작게` 만든다.

> 많은 일을 하는 테스팅 코드는 어렵다.’
> ‘많은 일을 하는 디버깅 코드는 어렵다.’
> 이 두 가지 문제의 해결법은 많은 일을 하지 않도록 코드를 작성하는 것이다. 각각의 함수를 단 한가지만의 일을 하도록 작성해야 한다. 이렇게 하면 단위 테스트로 쉽게 테스트할 수 있다. (하나의 함수에 대해 많은 단위 테스트가 필요하지 않는다.)
> 내 동료가 메소드를 더 작게 분리해야 하는지에 대해 판단할 때 사용하는 문구가 있다. 만약 코드의 역할을 다른 프로그래머에게 설명할 때 ‘and’라는 단어를 사용했다면 그 메소드는 적어도 하나 이상의 부분으로 나눠야 한다는 것이다. [stackoverflow](http://softwareengineering.stackexchange.com/questions/195989/is-it-ok-to-split-long-functions-and-methods-into-smaller-ones-even-though-they/195992#195992)

단위 테스트의 다른 장점은 문제를 빨리 발견하고 변화를 쉽게하며 통합을 간단하게 하고 설계를 개선할 수 있다는 것이다.

> 여기까지 읽고 나니, 테스트코드를 짜는 것은, 설계도를 만들면서 프로그래밍을 한다고 생각해도 될듯하다. 스케치를하는 느낌! 아키텍처를 짜면서 프로그래밍을 할 수 있다.

단위테스트의 시작은 어렵다. “hot to start unit testing”를 구글에 검색하면 113,000,000의 결과가 나올정도로. 단위테스트를 시작하는 가장 쉬운 방법은 버그를 고치는 것입니다. 그 방법은 아래와 같다.

- 버그를 찾는다.
- 버그를 고쳤을 때 통과할만한 테스트를 작성한다.
- 테스트를 통과할 때까지 코드를 수정한다.



<br><br>

## 2. 결국 클린코드를 위한 길

요구사항이 수시로 변경되고 프로젝트 일정이 눈에 보이기 시작하면 테스트 코드 없이 바로 로직 구현을 하는 경우가 태반이라고 했다. 물론 신속히 개발할 수 있지만, 나중에 유지 보수 때 문제가 발생한다고 한다. 유지보수라고 하지만 새로운 기능을 요구할 때도 있다. 기존 로직에 영향을 주지 않고 코드를 작성해야 하는데 테스트 코드 없이 구현하다 보면 사이드 이펙트가 여기 저기서 터진다. 결국 유지보수기간에 코드에 대한 테스트 코드를 다시 작성하는 사태..

### 클린코드가 우선순위에서 밀려나서는 안되는 3가지 이유 [+](http://codesquad.kr/page/specialTdd.html)

#### 1. 유지보수하기 좋은 코드

프로그래머 작업 중 가장 많은 시간을 투자하는 일이 유지보수이다. 유지보수하기 좋은 코드를 구현하는 것은 서비스의 성패를 좌우하는 중요한 요소이다. 유지보수하기 좋은 코드를 구현하는 핵심은 클린 코드 구현에 있다.

#### 2. 변화에 대응하는 핵심 능력

운영 중 필연적으로 발생하기 마련인 기능 추가, 버그 수정, 레거시 코드 리뷰(이전 개발자들의 유산과도 같은 코드) 및 수정 작업과 같은 변화 자체에 확장성있게 대응하려면 클린 코드에 대한 이해와 작성 능력이 필수. 변화를 피할 수 없으면 제대로 대응하자.

#### 3. 프로젝트 협업 능력 향상

읽기 좋은 클린 코드를 작성함으로서 오는 장점은 개인에게만 있지 않다. 특히 1인 개발이 아닌, 여러 개발자가 함께 참여하는 프로젝트일수록 확장성 있는 코드 작성 능력을 가진 개발자의 기여도는 높을 수 밖에 없다.

#### cf. 레거시 코드란 `Legacy code`

- 테스트가 불가능하거나 어려운 코드
- [클린코드를 위한 리팩토링](https://feel5ny.github.io/2017/12/09/Refactoring/)

------

<br><br>

## 3. TDD : Test Driven Development [+](http://blog.jeonghwan.net/mocha/)

1. 우선 개발자는 바라는 향상 또는 새로운 함수를 정읳하는 자동화된 테스트 케이스를 작성(초기적 결함을 점검하는)
2. 케이스를 통과하기 위한 최소한의 양의 코드를 생성
3. 새 코드를 표준에 맞도록 리팩토링

[![img](https://feel5ny.github.io/images/post_img/45/tdd-work-flow.gif)](https://feel5ny.github.io/images/post_img/45/tdd-work-flow.gif)일반적으로 RED, GREEN, REFACTOR 세단계를 거쳐 이루어진다.

> 참고한 글에서는 심리적인 안정감을 준다고 한다. 테스트 없이 개발하면 그물망 없는 막타워를 뛰어 내리는 심정이라면서.. 코드 한줄한줄이 부담스럽다고 한다.
> 생각해보면 이전 프로젝트에서는 테스트코드 없이 작업에 들어갔었고, 기능구현은 됐으나, 내가 보기 싫은 정도의 무자비한 코드들이 나왔다. 그러다보니 리팩토링조차 하지 못하는 상황.. 그런 의미에서 클린코드를 위한 작업이라고 생각하면, 좋을 듯하다.

------

### BDD (Behaviour Driven Development)

BDD는 행동 베이스이다.
엔드 유저의 행동을 시나리오로 가져야합니다.
가령 로그인을 예로 BDD를 하려면, 셀레늄으로 실제 브라우저에서 렌더링된 아이디와 패스워드를 입력폼에 넣고 로그인 버튼까지 누르는걸 시뮬레이트해야한다.

> Given, When, Then

```
Scenario: User uses wrong password  Given a username dkfksl;  And a password kdfksj;  When the user logs in with username and password  Then the login form should be shown again
```

1. 특정 값이 주어지고 (Given)
2. 어떤 이벤트가 발생했을 때 (When)
3. 그에 대한 결과를 보장해야한다 (Then)



<br><br>

## 4. 실습

> react 테스트때는 Enzyme과 묶어서 Mocha 사용할 예정.
> Typescript사용, type definition으로는 mocha, node, express 셋팅, 의존모듈 : mocha, shoudld, supertest, typeScript

실습을 위해 필요한 것과 알야아할 용어

- mocha
- should.js (assert 대체)
- supertest
- sinon
  - mock

------

### mocha

[![img](https://feel5ny.github.io/images/post_img/45/mocha.svg)](https://feel5ny.github.io/images/post_img/45/mocha.svg)Mocha is a feature-rich JavaScript test framework running on Node.js and in the browser, making asynchronous testing simple and fun. Mocha tests run serially, allowing for flexible and accurate reporting, while mapping uncaught exceptions to the correct test cases.

------

### should [+](http://shouldjs.github.io/)

- 테스트 코드를 검증할 때 사용하는 써드파티 라이브러리
- 가독성이 높은 테스트 코드를 만들 수 있게해주는 장점!
- assert는 nodejs 공식 모듈, should 써드파티 모듈
  - assert란 실행 결과를 비교하기위해 사용하는 내장 모듈.
  - 노드에서 제공하는 assert라는 라이브러리가 있지만, 노드측에서 발표를 했다! 노드 assert 이외의 서드파티 라이브러리를 사용하라..!!

```
res.body.should.be.a.Object();              // res.body는 Object 형태이어야한다. should.be.ares.body.should.have.property('result');    // res.body는 result라는 property를 갖고(have) 있어야 한다. res.body.result.should.have.equal('Hello Get TDD') // res.body.result는 'Hello Get TDD'라는 문자열과 같은 값을 갖고 있어야 한다.
```

------

### superTest [+](https://github.com/visionmedia/supertest)

- mocha가 하나의 함수를 테스트했다면, `통합테스트`(api 기능 테스트)에서 사용하는 라이브러리

- api 서버라면 내부적으로 express 구동 -> 요청보낸 뒤 결과 검증까지함(시나리오 코드 작성)

  ```
  const supertest = require('supertest');require('should');const server: any = supertest.agent('http://localhost:3000')describe('테스트 시작', () => {  it('GET', done => {    server.get('/').expect("Content-type", /json/).expect(200)      .end((err, res) => {        if (err) throw err;        res.body.should.be.a.Object();        res.body.should.have.property('result');        res.body.result.should.have.equal('Hello Get TDD')        done();      })  });
  ```

서버에서 구현하는 API는 단순히 메모리상의 데이터를 다루는 것만 있는 것은 아닙니다. 사용자 계정 정보 등 영구적으로 저장할 데이터는 데이터베이스에 저장하게 되는데, 서버에 이 정보를 데이터베이스에서 조회한 뒤 API로 응답하는 경우가 빈번합니다.
그럼 유닛 테스트를 위해 데이터베이스에 직접 자료를 넣어야 할까요? 그렇게 할 수도 있겠지만 시간이 많이 걸립니다. 왜냐하면 테스트를 위해 데이테베이스 컨넥션을 생성한 뒤 데이터를 입력하고 조회하는 시간이 상당하기 때문입니다. 게다가 이러한 API가 많아질수록 전체 테스트 시간은 늘어나게 됩니다.
그래서 목(Mock)을 사용해야 합니다. 데이터베이스 역할을 흉내낼 수 있는 가상의 것을 만들수 있습니다.

------

### sinon [+](https://www.slideshare.net/UyeongJu/javaScript-test-double-sinonjs)

Sinon.js는 주로 테스트 대상이 의존하는 모듈과 자원의 `대역`의 용도로 사용한다. 특정 테스트 프레임워크를 의존하지 않기 때문에 QUnit 이외의 테스트 프레임워크와도 함께 사용할 수 있다. Sinon이라는 이름은 트로이 전쟁에서 활양한 스파이의 이름에서 유래했다.
복잡한 비동기나 동시 다발적인 이벤트 그리고 애니메이션등 `작성하기 어려운 테스트 타입`을 Spy, Stub, Mock, FakeTiner, FakeServer 등으로 대체하여 테스트하기 쉽게 만들어 준다.

MOCK작업을 할 수 있게 하는 라이브러리라고 생각하면된다.
MOCK : 데이터 베이스 역할을 흉내내는 가상의 것

------

#### Mock이란?

실제 객체를 만들기엔 비용과 시간이 많이 들거나 의존성이 길게 걸쳐져 있어 제대로 구현하기 어려울 경우, 가짜 객체를 만들어 사용한다.

- JavaScript는 비동기 상황을 테스트해야하기 때문에, 실제로 테스트 코드를 짜는 것은 굉장히 까다로운 작업이 될수 있다.
- 이러한 것을 해결하기 위한 하나의 방법으로 stub, mock object를 만드는 방법이 있다.

------

#### Mock 객체는 언제 필요한가?

- 테스트 작성을 위한 환경 구축이 어려운 경우
  - 환경 구축을 위한 작업 시간이 많이 필요한 경우에 Mock객체를 사용한다. (데이터베이스, 웹서버, 웹애플리케이션서버, FTP서버, 등)
  - 특정 모듈을 갖고 있지 않아서 테스트 환경을 구축하지 못할 때 또는 타 부서와의 협의나 정책이 필요한 경우에 사용한다.
- 테스트가 특정 경우나 순간에 의존적인 경우
- 테스트 시간이 오래 걸리는 경우
- 개인 PC의 성능이나 서버의 성능문제로 오래 걸릴수 있는 경우 시간을 단축하기 위해 사용한다.

------

### cf. 테스트 러너 (karma)

JavaScript는 브라우저 환경에 따라, 테스트 결과가 달라지기 때문에, 실제 브라우저에서 테스트를 꼭! 해야만한다. 테스트 러너는 여러 환경에서 동일 테스트를 호출 할 수 있다.

------

참고링크

1. http://www.incodom.kr/Mock
2. http://webframeworks.kr/tutorials/expressjs/expressjs_test_code/
3. https://mochajs.org/
4. http://jinbroing.tistory.com/148
5. https://hyunseob.github.io/2016/05/09/assert-nodejs-test-module/
6. https://suhyeon.github.io/2017/12/10/TDD-1/
7. http://seokjun.kr/node-js-tdd/
8. http://blog.jeonghwan.net/mocha/
9. http://meetup.toast.com/posts/126
10. https://sungjk.github.io/2017/03/03/testing-react-application.html
11. [http://woogri.tistory.com/entry/BDD-BDD-%EB%84%88%EB%8A%94-%EC%96%B4%EB%94%94%EC%84%9C-%EC%98%A8%EA%B1%B0%EB%8B%88](http://woogri.tistory.com/entry/BDD-BDD-너는-어디서-온거니)