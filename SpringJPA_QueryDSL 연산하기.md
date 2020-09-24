# 2. QueryDSL 연산하기

### 2.1 casting

어떠한 수를 형변환 할 때 : `.castToNum(Class<A> type)` 을 하면 해당 type에 맞춰 형변환이 된다.

queryDSL은 수에 관련된 형태는 다 wrapper class 형으로 갖고 있음(Byte, Integer, Short, Long, Double, Float)

> 💡  `.castToNum(Class<A> type)`    
>  `NumberExpression` class 의 static method로 구현되어 있다

<br><br>

### 2.2 QueryResults

- fetchOne() || fetch() || fetchFirst() 와 다르게, 조회 결과값과 list count 값을 반환한다. pageable 구현할 때 요긴하게 사용된다고 함.
- 조회 결과값인 `List<item>` 을 보고 싶다면           : QueryResults.getResults();
- 조회결과의 수인 `(long) count` 을 보고 싶다면    : QueryResults.getTotal();

한번만 조회하면 되므로 DB connection을 줄일 수 있다👍 

<br><br>

### 2.3 SELECT 문 + SUM(CASE WHEN ... THEN ... OTHERWISE... END)

거의 통계용으로 쓸 것 같음.

- CaseBuilder 를 만들어서 쓴다.
- `when()` 에 조건
- `then()` 에 조건이 맞을 때의 값 → sum을 할 것이므로 `NumberExpression` 형태여야 함.
- `otherwise()` 에 조건이 맞지 않을 때의 값 → sum을 할 것이므로 `NumberExpression` 형태여야 함.
- `sum()`
- `as` alias 설정을 하지 않으면 QueryDSL에서 매핑 오류 난다.



### 2.4 예제 (2.1 + 2.3)

조건이 많지만, 중요한 조건으로   
가로 * 세로 의 값이 특정 값 이하 일 때 해당 컬럼의 duration 값을 다 더해준다.

```mysql
# mysql 쿼리문
SUM(
  case when 
    ci.HEIGHT * ci.WIDTH <= 50273600L then ci.DURATION 
    ELSE 0 
  END
)AS S_DURATION
```



```java
// java _ queryDSL
// 값이 커질 수 있으므로 p 값은 long 형태로 받는다.
new CaseBuilder()
      .when(ci.height.multiply(ci.width).castToNum(Long.TYPE)
              .loe(50273600L)
          .then(ci.duration.castToNum(Long.TYPE))
          .otherwise(0L)
          .sum()
      .as("sDuration")
```

