---
description: (bssd, "AFNS", "KICS", kicsSwMap)
---

# Esg261\_IrDcntRateBu\_Ytm.setIrDcntRateBu()

```java
// irCurve 단위로 반복     
for(Map.Entry<IrCurve, Map<Integer, IrParamSw>> 
    curveSwMap : paramSwMap.entrySet()) {
    
  IrCurve irCurve  = curveSwMap.getKey();
  String irCurveNm = irCurve.getIrCurveNm();
```



### 1. YTM 금리 사용&#x20;

```java
// 기본 무위험 커브 (ytm rate) 준비 
List<IrCurveYtm> ytmList 
= IrCurveYtmDao.getIrCurveYtm(bssd, irCurveNm);
```

```java
// 시나리오 단위로 반복 
for(Map.Entry<Integer, IrParamSw> swSce : curveSwMap.getValue().entrySet()) {
```

* 260번의 경우 동일한 base spot rate를 기반으로 가지고 들어와서 시나리오 별 스프레드 처리하기 때문에 시나리오별로 반복하기 전에 spot map을 만들었지만, &#x20;
* 261의 경우 충격 시나리오별로 ytm 에 가산할 충격스프레드가 다르므로 시나리오별로 ytm을 읽어 shock을 반영 후 spot rate로 환산한 후 spot map을 만드는 형태임.&#x20;
  * 두 작업 모두 sw.multIntRate 가 null이라면 결과는 같아야 함.&#x20;

#### 1-1. YTM  + YTM spread&#x20;

```java
// 1. ytm -> spot(disc) 변환 
// (ytm에 직접 스프레드를 반영, 10.0 추가된 up down 시나리오 산출 부분 확인)
List<IRateInput> ytmAddList 
= ytmList.stream()
      .map(s->s.addSpread(swSce.getValue().getYtmSpread()))
      .collect(Collectors.toList());
```

#### 1-2. spread반영된 YTM을 spot rate으로 변환 (job 150)

```java
 List<IrCurveSpot> spotList 
 = Esg150_YtmToSpotSw.createIrCurveSpot(ytmAddList,swSce.getValue())//sw.multIntRate
            .collect(Collectors.toList());

spotList.forEach(s-> s.setIrCurve(irCurve));
spotList.forEach(s-> log.info("zzzz : {},{}", swSce.getKey(), s.toString()));
```

#### 1-3. spot rate map에 담기&#x20;

```java
TreeMap<String, Double> spotMap 
= spotList.stream().collect(Collectors.toMap(IrCurveSpot::getMatCd
                                        , IrCurveSpot::getSpotRate
                                        , (k, v) -> k
                                        , TreeMap::new));
    
if(spotList.isEmpty()) {log.warn(
"No IR Curve Spot Data [BIZ: {}, IR_CURVE_NM: {}] in [{}] for [{}]"
      , applBizDv
      , irCurveNm,
       toPhysicalName(IrCurveSpot.class.getSimpleName())
       , bssd);
      continue;
    }
```



### 2. 조정항목 반영

#### 2-1. 유동성 프리미엄&#x20;

```java
// 2. 유동성 프리미엄 가져오기 
// (biz, irCurveNm) 만기별 유동성프리미엄 
Map<String, Double> irSprdLpMap 
  = IrSprdDao.getIrSprdLpBizList
            ( bssd
            , applBizDv
            , curveSwMap.getKey()
            , swSce.getKey())
  .stream().collect(Collectors.toMap
   (IrSprdLpBiz::getMatCd, IrSprdLpBiz::getLiqPrem));
```

#### 2-2. 금리 충격스프레드 (irmodel.AFNS)

* 기존 esg에서는 `applBizDv.equals(EApplBizDv.KICS)` 에 따라 다른 로직을 적용함.
  * KICS 인 경우 irModelNm=AFNS 인 금리 충격시나리오를 적용함
  * 그외 0 적용.

**TODO** : 결정론적 시나리오를 어떤 방식으로 적용할 것인지 결정이 필요함.&#x20;

다른 비즈니스 구분이라 하더라도 시나리오 번호를 채번하여 충격스프레드를 적용하고자 하는 경우 IR\_PARAM\_SW\_USR에 적용할 스프레드의 금리 모델을 알려줘야 함. 지금은 AFNS로 동일하게 적용하고 있음. &#x20;

```java
// 3. 금리 충격스프레드 가져오기 시나리오번호도 디폴트 처리가 필요할까? 없으면 에러인데.
Map<String, Double> irSprdShkMap 
  = IrSprdDao.getIrSprdAfnsBizList
            ( bssd
            , irModelNm
            , curveSwMap.getKey()
            , StringUtil.objectToPrimitive
              (swSce.getValue().getShkSprdSceNo(), 1))
            .stream().collect(Collectors.toMap
             (IrSprdAfnsBiz::getMatCd, IrSprdAfnsBiz::getShkSprdCont));
```

#### 2-3. 조정항목 반영&#x20;

```java
// 4. 시나리오 적용할 준비 : spotSceList copy 
List<IrCurveSpot> spotSceList 
  = spotList.stream().map(s -> s.deepCopy(s))
          .collect(Collectors.toList());
          
String fwdMatCd = swSce.getValue().getFwdMatCd();
 
if(!fwdMatCd.equals("M0000")) {	
   Map<String, Double> fwdSpotMap = irSpotDiscToFwdMap(bssd, spotMap, fwdMatCd);
   spotSceList.stream().forEach(s -> s.setSpotRate(fwdSpotMap.get(s.getMatCd())));
   }

String pvtMatCd = swSce.getValue().getPvtRateMatCd(); 
double pvtRate  = spotMap.getOrDefault(pvtMatCd, 0.0);		
double pvtMult  = swSce.getValue().getMultPvtRate();
double addSprd  = swSce.getValue().getAddSprd();
int    llp      = swSce.getValue().getLlp();
```

```java
// tenor (matCd) 단위로 반복 
for(IrCurveSpot spot : spotSceList) {

// 조건 추가 : LLP 이전까지만 반복     
if(Integer.valueOf(spot.getMatCd().substring(1))
                <= llp * MONTH_IN_YEAR) {
```

```java
IrDcntRateBu dcntRateBu = new IrDcntRateBu();

double baseSpot = pvtMult 
                * (spot.getSpotRate() - pvtRate) +  pvtRate + addSprd ;

double baseSpotCont = irDiscToCont(baseSpot);

double shkCont      = irSprdShkMap.getOrDefault(spot.getMatCd(), 0.0) ;
double lpDisc       = irSprdLpMap.getOrDefault(spot.getMatCd(), 0.0);
```

```java
double spotCont     = baseSpotCont + shkCont;
double spotDisc     = irContToDisc(spotCont);
double adjSpotDisc  = spotDisc + lpDisc;
double adjSpotCont  = irDiscToCont(adjSpotDisc);
```



### 3. 결과 매핑&#x20;

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



