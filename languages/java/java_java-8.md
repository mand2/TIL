# [Java 8] Java 8에서의 새로운 특징

> 사라바다님의 글임!!

안녕하세요. 오늘은 Java 8에서 lambda 다음으로 제가 많이 사용하는 Stream에 대해서 알아보도록 하겠습니다. 사실 이미 자바는 11버전을 향해 달리고 있지만 우리는 아직 낯선게 사실입니다. 그리고 많은 OpenSource에서 호환되지 않기도 하지요. 따라서 아직은 다른곳에서 많이 사용하는 Java 8 버전에 대해서 알아보도록 하겠습니다.

앞서 lambda에 대해서는 간략하게 2번에 걸쳐 설명을 진행했습니다. 그렇다면 다른 특징은 무엇이 있을지 한번 보도록 하겠습니다.

## Interface의 변화

Java 8 버전이전에는 interface는 public abstract methods만 허용했습니다. 하지만 Java 8 부터는 interface에서 static과 default 메서드를 사용할 수 있습니다.

### static method

```
public interface Vehicle {
    void run();
    static String producer() {
        return "Hyundai Vehicles";
    }
}
```

이렇게 static 메서드를 구현하여 사용할 수 있습니다. Client에서는 아래와 같이 사용할 수 있습니다.

```
String producer = Vehicle.producer();
```

### Default Method

Default 메서드는 `default` 키워드를 선언하여 사용할 수 있습니다. default로 선언된 메서드는 그냥 사용할 수도 있으며 override하여 사용할 수도 있습니다.

```
public interface Vehicle {
    void run();
    default String producer() {
        return "Hyundai Vehicles";
    }
}
```

이렇게 선언하며 아래와 같이 사용할 수 있습니다.

```
Vehicle vehicle = new VehicleImpl();
String producer = vehicle.producer(); // expeced Hyndai Vehicles
```

## 메서드 참조

### 예제 Class

```
public class User {

    private String name;

    public User(String name) {
        this.name = name;
    }

    public static boolean isRealUser(User user) {
        ...
    }
}
```

### static

lambda식에서 사용하는 메서드 참조는 더 짧고 가독성 있게 사용할 수 있도록 되었습니다. static 메서드라고 하면 **`ContainingClass::methodName`**의 형식으로 사용할 수 있습니다.

```
List<User> list = Arrays.asList(new User("Kim"), new User("Yeom"))
list.stream.anyMatch(u -> User.isRealUser(u));
```

위의 뜻은 list에서 한개의 요소를 탐색해가며 isRealUser라는 메서드가 만족하는지 보는 것입니다. 이러한 식은 아래와 같이 변경할 수 있습니다.

```
list.stream.anyMatch(User::isRealUser);
```

### instance type

static 메서드가 아니라고 하더라도 가능합니다. 위 예제의 isRealName 메서드가 User class의 메서드가 되었다고 했을 때 아래와 같이 가능한 것입니다..

```
User user = new User("Kim");
list.stream().anyMatch(user::isRealName);
```

### 생성자

`ClassName::new`의 형식으로 생성자를 나타낼 수 있습니다.

```
List<String> list = Arrays.asList("Kim", "Yeom");
list.stream.map(User::new);
```

위와 같이 처리한다면 Kim과 Yeom에 대한 User가 map을 통해 만들어 질 것입니다.

## Optional

Optional class는 Java 8에서 등장하여 NullPointerException의 발생가능성에 대한 코드에대해 도와줄 수 있습니다.

## 예제 1

```
List<String> list = getList();
List<String> listOpt = list != null ? list : new ArrayList<>();
```

이런코드가 있다고 하겠습니다. 이 코드는 getList에서 받아온 list가 null이라면 새로 인스턴스를 할당한다는 것입니다. 이러한 코드를 Optional을 이용하면 아래와 같이 변합니다.

```
List<String> listOpt = getList().orElseGet(() -> new ArrayList<>());
```

간결해 진것을 알 수 있습니다.

## 예제 2

좀 더 와닿는 예제를 보도록 하겠습니다.

```
User user = getUser();
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        String street = address.getStreet();
        if (street != null) {
            return street;
        }
    }
}
return "not specified";
```

이런 예제가 있습니다. user가 null인지 먼저 체크한 후 차례대로 address를 체크, street를 체크합니다. 코딩을 하다보면 위와같은 상황은 자주 마주치게 됩니다. 위 예제를 Optional을 사용하여 변경해보겠습니다.

```
Optional<User> user = Optional.ofNullable(getUser());
String result = user
  .map(User::getAddress)
  .map(Address::getStreet)
  .orElse("not specified");
```

2줄로 처리할 수 있도록 변경되었습니다.

## 마무리

이렇듯 java8에서는 우리가 불편하게 중복하여 사용하던 부분을 간략하게 사용할 수 있도록 많이 진화하였습니다. java8을 잘 사용한다면 코딩이 보다 쉽고 간결해 질 것입니다. 다음시간에는 Stream에 대해서 알아보도록 하겠습니다.

## 참조

https://www.baeldung.com/java-8-new-features

##### 출처 https://sabarada.tistory.com/39 