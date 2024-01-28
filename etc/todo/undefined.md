# 초기설정

1. github에서 소스 내려받기
2. project > properties > Java Build Path > Libraries&#x20;
   * 관련 lib 추가, H2 DB의 경우 h2.jar연결
3. project > Maven > update project
4. h2 db console (DB 띄우기)
5. src\com\gof\process\Main.java 스키마 설정
   * EsgConstant.TABLE\_SCHEMA = properties.getOrDefault("schema", "PUBLIC").toString().trim().toUpperCase();
6. src\com\gof\util\HibernateUtil.java DB 설정
   *   local 위치의 database file위치 및 계정설정

       ```java
       settings.put(Environment.URL,     "jdbc:h2:tcp://localhost/~/H2/ESG");
       settings.put(Environment.USER,    "NESG");	
       settings.put(Environment.PASS,    "test");	
       ```
7. 실행 arg : -Dtime=2023-12-31 -Dproperties=/D:\github\gesg\_db/gesg.properties
