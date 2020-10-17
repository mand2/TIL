# mysql 날짜/시간 관련 함수 뽀개기

MySQL 8.0 [문서](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_timestamp)를 읽고 날짜와 시간에 관련된 함수를 정리해보았다. (계속 업데이트 예정)

<br>



## 1. 현재시간?

- NOW() 
- SYSDATE()

NOW() 와 SYSDATE()의 반환형은 같다!

현재의 날짜및시간형태(DATE AND TIME), 즉 `YYYY-MM-DD hh:mm:ss` or `YYYYMMDDhhmmss` 의 형태로 반환한다. 쿼리문 문맥상 String으로 사용되면 전자로, 숫자형으로 사용되면 후자의 형태로 반환함.



NOW() 와 SYSDATE()는 다르다!

반환**형**(return type)이 같다고 했지 같은 **값**을 반환한다는 말이 아니다. 심지어 NOW는 해당 DB에 세팅된 TIMEZONE의 영향을 받는다.

NOW() 는 현재 현재 쿼리문이 실행되기 시작한 때를 기준으로 항상 같은 값을 반환한다. 즉 같은 쿼리문에 `NOW()` 를 두번 이상 쓰게 되어도 같은 값을 반환한다. (**trigger나 function 내부**에서는 해당 트리거/함수가 실행되기 시작한 시간을 반환.)

SYSDATE() 는 `SYSDATE()`자체를 호출한 시점을 반환한다. 즉 같은 쿼리문에 두번 이상 쓰게 될 때 다른 값을 반환한다. 이러한 특성 때문에 복제용 DB와 원본 DB와의 차이가 있고, DB logging으로 사용하기에는 부적합하다(물론 아예 쓰지 말라는 건 아님. `--sysdate-is-now` 라는 옵션을 써서 `SYSDATE()`를 `NOW()` 처럼 사용할 수 있음.)

```mysql
mysql> SELECT NOW(), SLEEP(2), NOW();
+---------------------+----------+---------------------+
| NOW()               | SLEEP(2) | NOW()               |
+---------------------+----------+---------------------+
| 2006-04-12 13:47:36 |        0 | 2006-04-12 13:47:36 |
+---------------------+----------+---------------------+

mysql> SELECT SYSDATE(), SLEEP(2), SYSDATE();
+---------------------+----------+---------------------+
| SYSDATE()           | SLEEP(2) | SYSDATE()           |
+---------------------+----------+---------------------+
| 2006-04-12 13:47:44 |        0 | 2006-04-12 13:47:46 |
+---------------------+----------+---------------------+
```



<br>
<br>

## 2. 그럼 TIMESTAMP()는 뭐야?

TIMESTAMP()는 `NOW()`의 영향을 받는다. SYSDATE() 영향❌❌❌   
- `TIMESTAMP(expr)`    
  DATE타입이나 DATETIME 타입을 입력 받아서 DATETIME 형태로 반환.   
- `TIMESTAMP(expr1, expr2)`       
  1) 첫번째 인자 값에 2)두번째 인자값을 더한 3) 값을 반환. 



```mysql
SELECT TIMESTAMP('2003-12-31');
-> '2003-12-31 00:00:00'
SELECT TIMESTAMP('2003-12-31 13:10:20','22:00:00');
-> '2004-01-01 11:10:20'
```





<br>
<br>

## 3. 그럼 CURDATE(), CURTIME()은 뭐야?

쉽게 생각하면 TIMESTAMP() 함수를 사용했을 때, 현재날짜를 CURDATE(), 현재시각을 CURTIME()으로 생각하면 된다.

`CURDATE()` 가 문맥상 String으로 사용되었다면 'YYYY-MM-DD'형태로, 숫자형으로 사용되었다면 'YYYYMMDD' 형태로 반환한다.

```mysql
SELECT CURDATE();     # String
-> '2008-06-13'
SELECT CURDATE() + 0; # Numeric
-> 20080613
```

`CURTIME()` 또한 String, Numeric(숫자형) 에 따라 반환형태가 다르다. _hh:mm:ss_ 혹은 _hhmmss_로 반환하게 됨. DB세팅할 때 정해진 session time zone에 따라 반환값이 정해지며, 소수점 6자리 내까지 반환하게 되어있다면 그 반환형태까지 따르게 된다. (소수점 4자리까지 반환하게 되어있다면 👉 204301.0012와 같이 나옴.) 



### 👉 같은 뜻을 가진 함수

- CURDATE()
  - CURRENT_DATE
  - CURRENT_DATE()
- CURTIME()
  - CURRENT_TIME
  - CURRENT_TIME()
- NOW()
  - CURRENT_TIMESTAMP
  - CURRENT_TIMESTAMP()







... 추가필요 ....

시간 계산 방법,    -> [stackoverflow](https://stackoverflow.com/questions/8544438/select-records-from-now-1-day)      
curtime curdate timestamp (등...)는 db에 얼마나 부하를 줄까?    
그 외 주의사항..?!?! 