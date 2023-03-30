# SRC

## job src 구성&#x20;

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
