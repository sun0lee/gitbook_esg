# job210

#### (Process) [ir-historical.md](../../../../biz-logic/esg-process/2.-adjusted-risk-free-term-structure/ir-shock-spread/ir-historical.md "mention")

## 0. 금리커브 단위로 반복&#x20;

```java
for(Map.Entry<String, IrCurve> irCrv : irCurveMap.entrySet())
```

{

## 1. check

```java
if(!irCurveSwMap.containsKey(irCrv.getKey())) {
  log.warn("No Ir Curve Data [{}] in Smith-Wilson Map for [{}]"
  , irCrv.getKey()
  , bssd);						
  continue; // 여기 걸리면 해당 반복부분만 탈출하고 다음번 반복을 이어서 !
}
```

## 2. tenorList&#x20;

작업기준일자의 base Tenor 목록을 추출함. (hist 내역에서도 동일한 tenor기준으로 조회할 목적 )

```java
List<String> tenorList = IrCurveSpotDao.getIrCurveTenorList
    ( bssd
    , irCrv.getKey()
    , Math.min(
           StringUtil.objectToPrimitive(irCurveSwMap.get(irCrv.getKey()).getLlp())
         , 20) // llp까지 
    );
    
log.info("TenorList in [{}]: ID: [{}], llp: [{}], matCd: {}"
   , jobLog.getJobId()
   , irCrv.getKey()
   , irCurveSwMap.get(irCrv.getKey()).getLlp()
   , tenorList
   );	
```

<details>

<summary>IrCurveSpotDao.getIrCurveTenorList()</summary>

```java
public static List<String> getIrCurveTenorList
		( String bssd
		, String irCurveNm
		, Integer llp)

String query = "select a.matCd from IrCurveSpot a             "
	 + " where 1=1                                        "
	 + "   and a.baseDate  =:baseYmd                      "
	 + "   and a.irCurveNm =:irCurveNm                    "
	 + "   and to_number(substr(a.matCd, 2)) <= :llp * 12 "
	 ;
	
return session.createQuery(query, String.class)
		.setParameter("baseYmd", getMaxBaseDate(bssd, irCurveNm))
		.setParameter("irCurveNm", irCurveNm)
		.setParameter("llp", llp)
		.getResultList();
}
```

* \[M0003, M0006, M0009, M0012, M0018, M0024, M0030, M0036, M0048, M0060, M0084, M0120, M0180, M0240]

</details>

## 3. delete

```java
int delNum = session.createQuery(
     " delete IrCurveSpotWeek a 
       where a.baseDate >= :param1 
       and a.baseDate <= :param2 
       and a.irCurveNm = :param3"
 )
	.setParameter("param1", iRateHisStBaseDate)
	.setParameter("param2", bssd+31)
	.setParameter("param3", irCrv.getKey()).executeUpdate();

log.info("[{}] has been Deleted in Job:[{}] [IR_CURVE_NM: {}, COUNT: {}]"
, Process.toPhysicalName(IrCurveSpotWeek.class.getSimpleName())
, jobLog.getJobId()
, irCrv.getKey()
, delNum);

```

## 4. spotWeek

```java
List<IrCurveSpotWeek> spotWeek = Esg210_SpotWeek.setupIrCurveSpotWeek
    ( bssd
    , iRateHisStBaseDate
    , irCrv.getKey()
    , tenorList
    );
    
spotWeek.stream().forEach(s -> s.setUpdateDate(LocalDateTime.now()));
spotWeek.stream().forEach(s -> s.setModifiedBy("ESG210")) ;
spotWeek.stream().forEach(s -> session.save(s));
```

## 5. save

```java
session.flush();
session.clear();
```

}

## 6. log

```java
completeJob("SUCCESS", jobLog);
```

&#x20;
