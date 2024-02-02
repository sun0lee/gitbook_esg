# H2 DB 저장경로 옮기기



### default 경로 : C:\Users\Sunyoung\H2

jdbc:h2:tcp://localhost/<mark style="background-color:yellow;">\~</mark>/H2/data/ESG



### 수정경로  :  D:\H2\data

jdbc:h2:tcp://localhost/d:/H2/data/ESG



### DBeaver Connection setting

JDBC URL : jdbc:h2:tcp://localhost/d:/H2/data/ESG

####

### 이클립스 설정 수정

```java
public class HibernateUtil {
settings.put(Environment.DRIVER,  "org.h2.Driver");
settings.put(Environment.DIALECT, "org.hibernate.dialect.H2Dialect");
// settings.put(Environment.URL,  "jdbc:h2:tcp://localhost/~/H2DB/db/ESG");			
settings.put(Environment.URL,     "jdbc:h2:tcp://localhost/d:/H2/data/ESG");
settings.put(Environment.USER,    "NESG");	
settings.put(Environment.PASS,    "test");	
log.info("getSesson Factory no Arg");
return genSessionFactory(settings);
```
