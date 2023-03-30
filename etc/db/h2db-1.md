---
description: 오라클과 달리 H2에서 지원하지 않는 함수는 아래처럼 추가해서 사용하면 됨.
---

# H2DB 미지원함수 추가

#### TO\_NUMBER 추가&#x20;

```sql
CREATE ALIAS TO_NUMBER AS '
@CODE
Long toNumber(String value) {
     return value == null ? null : Double.valueOf(value).longValue();
}
'
```

출처 : [https://stackoverflow.com/questions/48078994/oracle-function-to-number-not-supported-in-h2-database](https://stackoverflow.com/questions/48078994/oracle-function-to-number-not-supported-in-h2-database)



#### &#x20;TO\_DATE 추가&#x20;

```sql
CREATE ALIAS TO_DATE as '
import java.text.*;
@CODE
java.util.Date toDate(String s, String dateFormat) throws Exception { 
  return new SimpleDateFormat(dateFormat).parse(s); 
} 
' 
```

출처 : [https://stackoverflow.com/questions/21544114/function-to-date-not-found-in-h2-database](https://stackoverflow.com/questions/21544114/function-to-date-not-found-in-h2-database)
