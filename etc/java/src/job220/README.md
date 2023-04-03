# job220

(Process)[calculate-shock-spread.md](../../../../biz-logic/esg-process/2.-adjusted-risk-free-term-structure/ir-shock-spread/calculate-shock-spread.md "mention")

## 0.  금리커브 단위로 반복&#x20;

```java
for(Map.Entry<String, IrCurve> irCrv : irCurveMap.entrySet())
```

{

## 1. check&#x20;

### 1.1.irCurveSwMap

```java
if(!irCurveSwMap.containsKey(irCrv.getKey())) {
	log.warn("No Ir Curve Data [{}] in Smith-Wilson Map for [{}]"
	, irCrv.getKey()
	, bssd);
	continue;
}	
```

### 1.2. modelMstMap

```java
if(!modelMstMap.containsKey(irCrv.getKey())) {
	log.warn("No Model Attribute of [{}] for [{}] in [{}] Table"
	, irModelNm
	, irCrv.getKey()
	, Process.toPhysicalName(IrParamModel.class.getSimpleName()));
	continue;
}

log.info("AFNS Shock Spread (Cont) for [{}({}, {})]"
	, irCrv.getKey()
	, irCrv.getValue().getIrCurveNm()
	, irCrv.getValue().getCurCd());

```

### 1.3. spot rate에 기준일 테너별 데이터 존재 체크 &#x20;

```java
List<String> tenorList = IrCurveSpotDao.getIrCurveTenorList
( bssd
, irCrv.getKey()
, Math.min(StringUtil.objectToPrimitive(irCurveSwMap.get(irCrv.getKey()).getLlp())
	, 20)) ; // llp까지만

log.info("TenorList in [{}]: ID: [{}], llp: [{}], matCd: {}"
	, jobLog.getJobId()
	, irCrv.getKey()
	, irCurveSwMap.get(irCrv.getKey()).getLlp()
	, tenorList);

if(tenorList.isEmpty()) {
	log.warn("No Spot Rate Data [ID: {}] for [{}]"
		, irCrv.getKey()
		, bssd);
	continue;
}
```

## 2.delete&#x20;

### 2.1. IrParamAfnsCalc

```java
int delNum1 = session.createQuery("delete IrParamAfnsCalc a 
               where baseYymm=:param1 
               and a.irModelNm=:param2 
               and a.irCurveNm=:param3")
               			 .setParameter("param1", bssd) 
               			 .setParameter("param2", irModelNm)
               			 .setParameter("param3", irCrv.getKey())
               			 .executeUpdate();

log.info("[{}] has been Deleted 
               in Job:[{}] [IR_MODEL_ID: {}, IR_CURVE_NM: {}, COUNT: {}]"
               , Process.toPhysicalName(IrParamAfnsCalc.class.getSimpleName())
               , jobLog.getJobId()
               , irModelNm
               , irCrv.getKey()
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
                     .setParameter("param3", irCrv.getKey())
                     .executeUpdate(); 		

log.info("[{}] has been Deleted 
                     in Job:[{}] [IR_MODEL_ID: {}, IR_CURVE_NM: {}, COUNT: {}]"
                     , Process.toPhysicalName(IrSprdAfnsCalc.class.getSimpleName())
                     , jobLog.getJobId()
                     , irModelNm
                     , irCrv.getKey()
                     , delNum2);

```

## 3. 필요 데이터 준비 &#x20;

### 3.1.spot rate his (금리내역)

```java
List<IrCurveSpotWeek> weekHisList = IrCurveSpotWeekDao.getIrCurveSpotWeekHis
    ( bssd
    , iRateHisStBaseDate
    , irCrv.getKey()
    , tenorList
    , weekDay
    , false);
List<IrCurveSpotWeek> weekHisBizList = IrCurveSpotWeekDao.getIrCurveSpotWeekHis
    ( bssd
    , iRateHisStBaseDate
    , irCrv.getKey()
    , tenorList
    , weekDay
    , true);
log.info("weekHisList: [{}], [TOTAL: {}, BIZDAY: {}], [from {} to {}, weekDay:{}]"
    , irCrv.getKey()
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
	, irCrv.getKey()
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
				(bssd, irCrv.getKey(), tenorList);

if(curveBaseList.size()==0) {
	log.warn("No IR Curve Data [IR_CURVE_NM: {}] for [{}]"
	, irCrv.getKey()
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
  , tenorList
  , modelMst  // add 
  , dt
  , sigmaInit
  , irCurveSwMap.get(irCrv.getKey()).getLtfr()
  , irCurveSwMap.get(irCrv.getKey()).getLtfrCp() 
  , projectionYear
  , errorTolerance
  , kalmanItrMax
  , confInterval
  , epsilonInit);
```

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
