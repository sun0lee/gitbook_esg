# job220

(Process)[calculate-shock-spread.md](../../../../biz-logic/esg-process/2.-adjusted-risk-free-term-structure/ir-shock-spread/calculate-shock-spread.md "mention")

## 0.  금리커브 단위로 반복&#x20;

```java
for (IrCurve irCurve :irCurveList) {

// 내부변수 add
    String        irCurveNm  = irCurve.getIrCurveNm() ;
    IrParamSw     irparamSw  = commIrParamSw.get(irCurveNm) ;
    IrParamModel  irModelMst = irModelMstMap.get(irCurveNm) ;
```



## 1. check&#x20;

### 1.1.irCurveSwMap

```java
if(!commIrParamSw.containsKey(irCurveNm)) {
    log.warn("No Ir Curve Data [{}] in Smith-Wilson Map for [{}]"
            , irCurveNm
            , bssd);
    continue;
}
```

### 1.2. modelMstMap

```java
if(!irModelMstMap.containsKey(irCurveNm)) {
    log.warn("No Model Attribute of [{}] for [{}] in [{}] Table"
      , irModelNm
      , irCurveNm
      , Process.toPhysicalName(IrParamModel.class.getSimpleName()));
    continue;
}

log.info("AFNS Shock Spread (Cont) for [{}({}, {})]"
      , irCurveNm
      , irCurve.getIrCurveNm()
      , irCurve.getCurCd()
      );
```

### 1.3. spot rate에 기준일 테너별 데이터 존재 체크 &#x20;

```java
List<String> tenorList = IrCurveSpotDao.getIrCurveTenorList
     ( bssd
     , irCurveNm
     , Math.min(irparamSw.getLlp(), 20)
     );

log.info("TenorList in [{}]: ID: [{}], llp: [{}], matCd: {}"
	, jobLog.getJobId()
	, irCurveNm
	, Math.min(irparamSw.getLlp(), 20)
	, tenorList
	);

if(tenorList.isEmpty()) {
	log.warn("No Spot Rate Data [ID: {}] for [{}]", irCurveNm, bssd);
	continue;
}
```

## 2.delete&#x20;

### 2.1. IrParamAfnsCalc

```java
int delNum1 = session.createQuery(
              "delete IrParamAfnsCalc a 
               where baseYymm=:param1 
               and a.irModelNm=:param2 
               and a.irCurveNm=:param3")
               		.setParameter("param1", bssd) 
               		.setParameter("param2", irModelNm)
               		.setParameter("param3", irCurveNm)
               		.executeUpdate();

log.info("[{}] has been Deleted 
               in Job:[{}] [IR_MODEL_ID: {}, IR_CURVE_NM: {}, COUNT: {}]"
               , Process.toPhysicalName(IrParamAfnsCalc.class.getSimpleName())
               , jobLog.getJobId()
               , irModelNm
               , irCurveNm
               , delNum1);

```

### 2.2.IrSprdAfnsCalc

```java
int delNum2 = session.createQuery("delete IrSprdAfnsCalc a 
              where baseYymm=:param1 
              and a.irModelNm=:param2 
              and a.irCurveNm=:param3")
                     .setParameter("param1", bssd) 
                     .setParameter("param2", irModelNm)
                     .setParameter("param3", irCurveNm)
                     .executeUpdate(); 		

log.info("[{}] has been Deleted 
                     in Job:[{}] [IR_MODEL_ID: {}, IR_CURVE_NM: {}, COUNT: {}]"
                     , Process.toPhysicalName(IrSprdAfnsCalc.class.getSimpleName())
                     , jobLog.getJobId()
                     , irModelNm
                     , irCurveNm
                     , delNum2);

```

## 3. 필요 데이터 준비 &#x20;

### 3.1.spot rate his (금리내역)

```java
List<IrCurveSpotWeek> weekHisList = IrCurveSpotWeekDao.getIrCurveSpotWeekHis
    ( bssd
    , iRateHisStBaseDate
    , irCurveNm
    , tenorList
    , weekDay
    , false);
List<IrCurveSpotWeek> weekHisBizList = IrCurveSpotWeekDao.getIrCurveSpotWeekHis
    ( bssd
    , iRateHisStBaseDate
    , irCurveNm
    , tenorList
    , weekDay
    , true);
log.info("weekHisList: [{}], [TOTAL: {}, BIZDAY: {}], [from {} to {}, weekDay:{}]"
    , irCurveNm
    , weekHisList.size()
    , weekHisBizList.size()
    , iRateHisStBaseDate.substring(0,6)
    , bssd
    , weekDay);			
```

### 3.2. 충분성 검토&#x20;

```java
if(weekHisList.size() < 1000) {
	log.warn("Weekly SpotRate Data is not Enough [ID: {}, SIZE: {}] for [{}]"
	, irCurveNm
	, weekHisList.size()
	, bssd);
	continue;
}

List<IrCurveSpot> curveHisList= weekHisList.stream()
	     .map(s->s.convertToHis())
	     .collect(toList());
```

### 3.3. 당월 금리 데이터 (모수)

AFNS 모형에서 모수 추정시 제약조건으로 현재 금리커브와 동일한지 확인용. &#x20;

```java
//Any curveBaseList result in same parameters and spreads.
List<IrCurveSpot> curveBaseList = IrCurveSpotDao.getIrCurveSpot
				(bssd, irCurveNm, tenorList);

if(curveBaseList.size()==0) {
	log.warn("No IR Curve Data [IR_CURVE_NM: {}] for [{}]"
	, irCurveNm
	, bssd);
	continue;
}
```

## 4. biz logic&#x20;

```java
Map<String, List<?>> irShockSenario = new TreeMap<String, List<?>>();
irShockSenario = Esg220_ShkSprdAfns.createAfnsShockScenario
  ( FinUtils.toEndOfMonth(bssd)
  , curveHisList
  , curveBaseList
  , irModelMst  // add 
  , irparamSw   // add 
  , argInDBMap  // add 
  );	
```

[#esg220\_shksprdafns](../../../../biz-logic/interest-rate-model/afns/afnelsonsiegel.md#esg220\_shksprdafns "mention")



## 5. save&#x20;

```java
for(Map.Entry<String, List<?>> rslt : irShockSenario.entrySet()) {
	rslt.getValue().forEach(s -> session.save(s));

session.flush();
session.clear();
}
```

}

## 6.log&#x20;

```java
completeJob("SUCCESS", jobLog);
```

##
