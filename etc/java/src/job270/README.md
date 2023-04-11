# job270

#### (process) [df-full-tenor.md](../../../../biz-logic/esg-process/2.-adjusted-risk-free-term-structure/bottom-up-discount-rate/df-full-tenor.md "mention")

## 1.  delete&#x20;

```java
int delNum = session.createQuery("delete IrDcntRate a where a.baseYymm=:param")
            .setParameter("param", bssd).executeUpdate();	
log.info("[{}] has been Deleted in Job:[{}] [BASE_YYMM: {}, COUNT: {}]"
            , Process.toPhysicalName(IrDcntRate.class.getSimpleName())
            , jobLog.getJobId()
            , bssd
            , delNum);
```

## 2.  biz logic&#x20;

```java
for(EApplBizDv biz : EApplBizDv.getUseBizList()) {
  
  List<IrDcntRate> bizDcntRate = Esg270_IrDcntRate.createIrDcntRate(bssd,  biz, bizIrParamSw.get(biz), projectionYear);
  bizDcntRate.stream().forEach(s -> session.save(s));
}
```

{% content-ref url="esg270_irdcntrate.createirdcntrate.md" %}
[esg270\_irdcntrate.createirdcntrate.md](esg270\_irdcntrate.createirdcntrate.md)
{% endcontent-ref %}

<details>

<summary></summary>

```java
List<IrDcntRate> kicsDcntRate = Esg270_IrDcntRate.createIrDcntRate(bssd, "KICS", kicsSwMap, projectionYear);
    kicsDcntRate.stream().forEach(s -> session.save(s));

List<IrDcntRate> ifrsDcntRate = Esg270_IrDcntRate.createIrDcntRate(bssd, "IFRS", ifrsSwMap, projectionYear);
    ifrsDcntRate.stream().forEach(s -> session.save(s));

List<IrDcntRate> ibizDcntRate = Esg270_IrDcntRate.createIrDcntRate(bssd, "IBIZ", ibizSwMap, projectionYear);
    ibizDcntRate.stream().forEach(s -> session.save(s));

List<IrDcntRate> saasDcntRate = Esg270_IrDcntRate.createIrDcntRate(bssd, "SAAS", saasSwMap, projectionYear);
    saasDcntRate.stream().forEach(s -> session.save(s));
```



</details>

## 3. save&#x20;

```java
session.flush();
session.clear();
```

## 4.log			&#x9;

```java
completeJob("SUCCESS", jobLog);
```

