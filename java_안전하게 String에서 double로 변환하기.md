# safely parsing String to double

cf. Int 는 exception-safe 할 수 있는 메서드 있음 : `java.lang.Math.toIntExtract(long value)`

참고 주소 :  https://www.baeldung.com/java-string-to-double 





## **2. *Double.parseDouble***

We can convert a *String* to a *double* using the *Double.**parseDouble*** method:

```java
assertEquals(1.23, Double.parseDouble("1.23"), 0.000001);
```



## **3. *Double.valueOf***

Similarly, we can convert a *String* into a [boxed *Double*](https://www.baeldung.com/java-generics#generics-primitive-data-types) using the *Double.valueOf* method:

```java
assertEquals(1.23, Double.valueOf("1.23"), 0.000001);
```

Note that the returned value of *Double.valueOf* is a boxed *Double*. Since Java 5, this boxed *Double* is converted by the compiler to a primitive *double* where needed.

In general, **we should favor *Double.parseDouble*** since it does not require the compiler to perform auto-unboxing.



## **4. *DecimalFormat.parse***

When a *String* representing a *double* has a more complex format, we can use a *DecimalFormat*.

For example, we can convert a decimal-based currency value without removing non-numeric symbols:

```java
DecimalFormat format = new DecimalFormat("\u00A4#,##0.00");
format.setParseBigDecimal(true);
 
BigDecimal decimal = (BigDecimal) format.parse("-$1,000.57");
 
assertEquals(-1000.57, decimal.doubleValue(), 0.000001);
```

Similar to the *Double.valueOf*, the ***DecimalFormat.parse* method returns a *Number***, which we can convert to a primitive *double* using the *doubleValue* method. Additionally, we use the *setParseBigDecimal* method to force *DecimalFormat.parse* to return a *BigDecimal*.

Usually, the *DecimalFormat* is more advanced than we require, thus, we should favor the *Double.parseDouble* or the *Double.valueOf* instead.

To learn more about *DecimalFormat*, please check [a practical guide to *DecimalFormat*](https://www.baeldung.com/java-decimalformat).





