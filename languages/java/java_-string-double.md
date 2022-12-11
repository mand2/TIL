# safely parsing String to double

cf. Int 는 exception-safe 할 수 있는 메서드 있음 : `java.lang.Math.toIntExtract(long value)`

참고 주소 : https://www.baeldung.com/java-string-to-double

## **2. **_**Double.parseDouble**_

We can convert a _String_ to a _double_ using the _Double.**parseDouble**_ method:

```java
assertEquals(1.23, Double.parseDouble("1.23"), 0.000001);
```

## **3. **_**Double.valueOf**_

Similarly, we can convert a _String_ into a [boxed _Double_](https://www.baeldung.com/java-generics#generics-primitive-data-types) using the _Double.valueOf_ method:

```java
assertEquals(1.23, Double.valueOf("1.23"), 0.000001);
```

Note that the returned value of _Double.valueOf_ is a boxed _Double_. Since Java 5, this boxed _Double_ is converted by the compiler to a primitive _double_ where needed.

In general, **we should favor **_**Double.parseDouble**_ since it does not require the compiler to perform auto-unboxing.

## **4. **_**DecimalFormat.parse**_

When a _String_ representing a _double_ has a more complex format, we can use a _DecimalFormat_.

For example, we can convert a decimal-based currency value without removing non-numeric symbols:

```java
DecimalFormat format = new DecimalFormat("\u00A4#,##0.00");
format.setParseBigDecimal(true);
 
BigDecimal decimal = (BigDecimal) format.parse("-$1,000.57");
 
assertEquals(-1000.57, decimal.doubleValue(), 0.000001);
```

Similar to the _Double.valueOf_, the _**DecimalFormat.parse**_** method returns a **_**Number**_, which we can convert to a primitive _double_ using the _doubleValue_ method. Additionally, we use the _setParseBigDecimal_ method to force _DecimalFormat.parse_ to return a _BigDecimal_.

> Usually, the DecimalFormat is more advanced than we require, thus, we should favor the Double.parseDouble or the Double.valueOf instead.

`코멘트` : DecimalFormat은 엑셀의 셀형식 지정과 비슷한 개념임. 형식을 갖춘 값을 만들고 싶다면 쓰면 됨. 형식 지정 없이 그냥 double 형으로만 갖고오고 싶다면, _Double.parseDouble_ or the _Double.valueOf_ 을 사용하길 바람. Double.parseDouble 또한 numberformatexception 이었나.. 예외 처리될 수 있음.

To learn more about _DecimalFormat_, please check [a practical guide to _DecimalFormat_](https://www.baeldung.com/java-decimalformat).
