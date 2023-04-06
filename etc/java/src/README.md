# SRC

## 1. ESG 실행&#x20;

모든 작업은 main class에서 실행함. main에서는 작업마다 공통적으로 수행하는 작업을 미리 구성하거나 필요한 필드(상수)를 정의함.&#x20;

세부적인 작업은 main에서 call 하는 형태로 실행되며, 코드의 layout은 아래와 같음.&#x20;



## 2. job src 구성&#x20;

이하 목차의 job 단위 세부 로직은 try{ } 안의 로직을 정리함.&#x20;

```java
// 실행대상에 해당 작업이 포함되는지 체크 
if(jobList.contains("jobNo")) {
  
  // begin 트랜젝션 
  session.beginTransaction();
  
  CoJobInfo jobLog = startJogLog(EJob.ESGjobNo);	
  // 내부 변수 정의 
  
  try { 
  // biz logic 
    completeJob("SUCCESS", jobLog);	
   }
  
// 작업 중 에러 발생시 로그 
 catch (Exception e) {
	log.error("ERROR: {}", e);
	completeJob("ERROR", jobLog);
  }
  		
  session.saveOrUpdate(jobLog);
  session.getTransaction().commit();
  
}
```
