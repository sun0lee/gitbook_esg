---
description: (bssd, irModelNm, "IFRS", ifrsSwMap)
---

# Esg260\_IrDcntRateBu.setIrDcntRateBu



```java
// irCurve 단위로 반복 
for(Map.Entry<String, Map<Integer, IrParamSw>> 
    curveSwMap : paramSwMap.entrySet()) {
```

```java
// 기본 무위험 커브 (spot rate) 준비 
List<IrCurveSpot> spotList 
= IrCurveSpotDao.getIrCurveSpot(bssd, curveSwMap.getKey());
```

```java
// map (만기, spotRate) => lp, shock 등 충격 스프레드 적용위해 
TreeMap<String, Double> spotMap 
  = spotList.stream().collect(Collectors.toMap
          (IrCurveSpot::getMatCd, IrCurveSpot::getSpotRate
          , (k, v) -> k
          , TreeMap::new));

// 비어 있으면 알려주고 다음 금리커브 작업 반복 
if(spotList.isEmpty()) {
  log.warn("No IR Curve Spot Data [BIZ: {}, IR_CURVE_NM: {}] in [{}] for [{}]"
        , applBizDv
        , curveSwMap.getKey()
        , toPhysicalName(IrCurveSpot.class.getSimpleName())
        , bssd);
  continue;
}
```

```java
// 시나리오 단위로 반복 
for(Map.Entry<Integer, IrParamSw> 
    swSce : curveSwMap.getValue().entrySet()) {
```

```java
// 적용 스프레드 준비  
// (biz, irCurveNm) 만기별 유동성프리미엄 
Map<String, Double> irSprdLpMap 
  = IrSprdDao.getIrSprdLpBizList
            ( bssd
            , applBizDv
            , curveSwMap.getKey()
            , swSce.getKey())
  .stream().collect(Collectors.toMap
   (IrSprdLpBiz::getMatCd, IrSprdLpBiz::getLiqPrem));

// (biz, irCurveNm) 만기별 충격스프레드 
Map<String, Double> irSprdShkMap 
  = IrSprdDao.getIrSprdAfnsBizList
            ( bssd
            , irModelNm
            , curveSwMap.getKey()
            , StringUtil.objectToPrimitive(swSce.getValue().getShkSprdSceNo(), 1))
  .stream().collect(Collectors.toMap
   (IrSprdAfnsBiz::getMatCd, IrSprdAfnsBiz::getShkSprdCont));
```

```java
// spot rate (결과 담을 통)
List<IrCurveSpot> spotSceList 
  = spotList.stream().map(s -> s.deepCopy(s))
          .collect(Collectors.toList());

String fwdMatCd
 = StringUtil.objectToPrimitive(swSce.getValue().getFwdMatCd(), "M0000");
 
if(!fwdMatCd.equals("M0000")) {	
      Map<String, Double> fwdSpotMap = irSpotDiscToFwdMap(bssd, spotMap, fwdMatCd);
      spotSceList.stream().forEach(s -> s.setSpotRate(fwdSpotMap.get(s.getMatCd())));
    }
    
String pvtMatCd = StringUtil.objectToPrimitive(swSce.getValue().getPvtRateMatCd() , "M0000");
double pvtRate  = StringUtil.objectToPrimitive(spotMap.getOrDefault(pvtMatCd, 0.0), 0.0    );
double pvtMult  = StringUtil.objectToPrimitive(swSce.getValue().getMultPvtRate()  , 1.0    );
double addSprd  = StringUtil.objectToPrimitive(swSce.getValue().getAddSprd()      , 0.0    );
int    llp      = StringUtil.objectToPrimitive(swSce.getValue().getLlp()          , 20     );
```

```java
// tenor (matCd) 단위로 반복 
for(IrCurveSpot spot : spotSceList) {

    // 조건 추가 : LLP 이전까지만 반복     
    if(Integer.valueOf(spot.getMatCd().substring(1)) <= llp * MONTH_IN_YEAR) {
```

```java
IrDcntRateBu dcntRateBu = new IrDcntRateBu();

// 분석년도마다 예외적인 추가 시나리오가 있었던 듯. 
// int kicsAddSprdContSceNo = 12;
// if(bssd.equals("202012")) kicsAddSprdContSceNo =  6;
// if(bssd.equals("202112")) kicsAddSprdContSceNo = 12;


// pvtRate doesn't have an effect on parallel shift(only addSprd)
double baseSpot = pvtMult 
                * (StringUtil.objectToPrimitive(spot.getSpotRate()) - pvtRate) 
                +  pvtRate + addSprd  ; 

double baseSpotCont = irDiscToCont(baseSpot);

double shkCont 
  = (applBizDv.equals("KICS") && swSce.getKey() <= kicsAddSprdContSceNo) ?
                   irSprdShkMap.getOrDefault(spot.getMatCd(), 0.0) + addSprd 
                 : irSprdShkMap.getOrDefault(spot.getMatCd(), 0.0);
                 
double lpDisc       = irSprdLpMap.getOrDefault(spot.getMatCd(), 0.0);

double spotCont     = baseSpotCont + shkCont;
double spotDisc     = irContToDisc(spotCont);	
double adjSpotDisc  = spotDisc + lpDisc;
double adjSpotCont  = irDiscToCont(adjSpotDisc);

```

```java
// 결과값 담기 
dcntRateBu.setBaseYymm(bssd);
dcntRateBu.setApplBizDv(applBizDv);
dcntRateBu.setIrCurveNm(curveSwMap.getKey());
dcntRateBu.setIrCurve(spot.getIrCurve()); 
dcntRateBu.setIrCurveSceNo(swSce.getKey());
dcntRateBu.setMatCd(spot.getMatCd());
dcntRateBu.setSpotRateDisc(spotDisc);
dcntRateBu.setSpotRateCont(spotCont);
dcntRateBu.setLiqPrem(lpDisc);
dcntRateBu.setAdjSpotRateDisc(adjSpotDisc);
dcntRateBu.setAdjSpotRateCont(adjSpotCont);
dcntRateBu.setAddSprd(addSprd);
dcntRateBu.setModifiedBy(jobId);
dcntRateBu.setUpdateDate(LocalDateTime.now());	

rst.add(dcntRateBu);
```

} &#x20;

```java
// 모든 반복 작업이 정상적으로 끝나면 로그 출력
log.info("{}({}) creates [{}] results of [{}]." +
        "They are inserted into [{}] Table", jobId
   , EJob.valueOf(jobId).getJobName()
   , rst.size()
   , applBizDv
   , toPhysicalName(IrDcntRateBu.class.getSimpleName()));

return rst;
```

