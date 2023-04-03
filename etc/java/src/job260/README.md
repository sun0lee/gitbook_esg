# job260

#### (process) [df-base-tenor.md](../../../../biz-logic/esg-process/2.-adjusted-risk-free-term-structure/bottom-up-discount-rate/df-base-tenor.md "mention")

## 1.delete&#x20;

```java
int delNum = session.createQuery("delete IrDcntRateBu a where a.baseYymm=:param")
        .setParameter("param", bssd).executeUpdate();				
log.info("[{}] has been Deleted in Job:[{}] [BASE_YYMM: {}, COUNT: {}]"
        , Process.toPhysicalName(IrDcntRateBu.class.getSimpleName())
        , jobLog.getJobId()
        , bssd
        , delNum);
```

## 2. biz logic&#x20;

```java
String irModelNm = "AFNS";//for acquiring AFNS Shock Spread		

List<IrDcntRateBu> kicsDcntRateBu = Esg261_IrDcntRateBu_Ytm.setIrDcntRateBu(bssd, irModelNm, "KICS", kicsSwMap);				
kicsDcntRateBu.stream().forEach(s -> session.save(s));

List<IrDcntRateBu> ifrsDcntRateBu = Esg260_IrDcntRateBu.setIrDcntRateBu(bssd, irModelNm, "IFRS", ifrsSwMap);
ifrsDcntRateBu.stream().forEach(s -> session.save(s));

List<IrDcntRateBu> ibizDcntRateBu = Esg260_IrDcntRateBu.setIrDcntRateBu(bssd, irModelNm, "IBIZ", ibizSwMap);
ibizDcntRateBu.stream().forEach(s -> session.save(s));

//forward curve or manual shift of term structure is treated here
List<IrDcntRateBu> saasDcntRateBu = Esg260_IrDcntRateBu.setIrDcntRateBu(bssd, irModelNm, "SAAS", saasSwMap);
saasDcntRateBu.stream().forEach(s -> session.save(s));
```

## 3. save&#x20;

```java
session.flush();
session.clear();
```

## 4.log

```java
completeJob("SUCCESS", jobLog);
```

