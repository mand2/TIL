### mysql_decimal

정확한 값을 DB에 넣기 위해선 DECIMAL 사용(float, double 은 반올림이 될 수 있는 위험이 있기 때문에 정확한 값을 활용해야 하는 경우는 DECIMAL 사용)

```
column_name  DECIMAL(P,D);
```

In the syntax above:

- P is the precision that represents the number of significant digits. The range of P is 1 to 65.
- D is the scale that that represents the number of digits after the decimal point. The range of D is 0 and 30. MySQL requires that D is less than or equal to (<=) P.

The `DECIMAL(P,D)` means that the column can **store up to P digits with D decimals**. The actual range of the decimal column depends on the precision and scale.



> 주의!!! DECIMAL(6, 2) 는 전체 8자리를 세팅하는게 아니라 
> **전체 자리수 6자리**이면서 + 소수점자리 2자리가 포함됨. (정수 4 + 소수 2)
>
> 즉 XXXX.00 이런 형태가 최대임.





