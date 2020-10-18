## Item 5: 의존객체 주입 사용하기

A라는 클래스가 B객체를 의존해야 할 때 사용. 정적 팩터리(item 1), 빌더패턴(item 2)에서도 응용가능.

팩터리 메서드 패턴에서 응용하면 좋다!

- 주의사항

  **명시한 클래스 타입**(해당 클래스의 하위타입 포함)만 주입되도록 해야 함.

  의존성이 많을 땐 관리하기 어려워짐 → Spring framework에서는 어느정도 의존성side-effect 해결

  ```java
  public class SpellChecker {
  	private final Lexicon dictionary;
  
  	public SpellChecker(Lexicon dictionary) {
  		this.dictionary = Objects.requireNonNull(dictionary);
  	}
  	
  	public boolean isValid(String word) {...}
  	public List<String> suggestions(String typo) {...}
  }
  ```

## Item 8, 9 gc를 용이하게 하기위해

- finalizer, cleaner 사용을 가급적 피하고
- try-with—resources 사용하기

왜 그래야 하나?

1. jvm의 gc 처리시간 보장 | 메모리 누수 방지

   finalizer, cleaner에 의존하면 jvm이 gc처리하는 데 얼마나 시간이 걸릴 지 예측하기 더 어려워짐. 메모리 누수가 발생하게 되고, 성능에 영향을 미친다. 주의!!

   또한 `InputStream` `OutputStream` `java.sql.Connection` 등 제대로 자원을 닫아주어야 하는 클래스는 `try-finally`를 사용하면 제대로 닫아준다는 보장이 안되므로 `try-with-resources`를 사용하는 걸 추천! 👉 물론 일정시간 지나서 닫히지 않으면 **finalizer가 실행**되지만, finalizer의 priority가 높지 않음. 언제 처리될 지 불안한 상태가 된다.

2. 에러로그 찾기가 수월해진다

   그리고 `close()` 를 꼭 써주어야 하는 클래스들이 있는데, 중간에 exception이 발생하면 에러 로그 찾기가 번거로워 질 가능성이 있기 때문에 `try-with-resources`로 감싸주면 어디서 에러가 났는지 찾는데 수월해질 수 있다.

## Item 10, 11 논리적으로 같은 객체는 equals, hashCode 재정의하기

재정의해야할 때: **논리적 동치성**을 확인해야 할 때.

- `equals`는 일반 규약을 지켜 재정의하기

- `equals`가 재정의되면 `hashCode`도 재정의해야한다. ⇒ 논리적 동치성을 확보해야 함. 
- `equals(Object)` 가 같다고 판단:  
  두 객체의 hashCode 값은 똑같아야 한다.
  
  - `equals(Object)` 가 다르다다고 판단:   
두 객체의 hashCode 값이 무조건 달라야 하는 건 아님. 같아도 괜찮다.

세상에 홀로 존재하는 클래스는 없다. 전달받은 객체가 equals규약을 지킨다고 가정하고 동작하기 때문에, 내가 만든 클래스가 equals규약을 지키도록 구현해야 한다.

### equals 규약이란?

- `equals 규약` 5가지 
  - 반사성
  - 대칭성 🌟
  - 추이성 🌟
  - 일관성 🌟
  - null-아님