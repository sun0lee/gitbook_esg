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

```java
// 기본 무위험 커브 (ytm rate) 준비 
List<IrCurveYtm> ytmList 
= IrCurveYtmDao.getIrCurveYtm(bssd, irCurveNm);
```

```java
// 시나리오 단위로 반복 
for(Map.Entry<Integer, IrParamSw> 
    swSce : curveSwMap.getValue().entrySet()) {
```

```java
// 1. ytm -> spot(cont) 변환 
// (ytm에 직접 스프레드를 반영, 10.0 추가된 up down 시나리오 산출 부분 확인)
List<IRateInput> ytmAddList 
= ytmList.stream()
      .map(s->s.addSpread(swSce.getValue().getYtmSpread()))
      .collect(Collectors.toList());

 List<IrCurveSpot> spotList 
 = Esg150_YtmToSpotSw.createIrCurveSpot(ytmAddList, swSce.getValue())
                  .stream().map(s-> s.convertToCont()) //연속복리이율 형태로 get 
                  .collect(Collectors.toList());

spotList.forEach(s-> s.setIrCurve(irCurve));
spotList.forEach(s-> log.info("zzzz : {},{}", swSce.getKey(), s.toString()));
```

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
//pvtRate doesn't have an effect on parallel shift(only addSprd)

double baseSpotCont = baseSpot;	
double shkCont      = applBizDv.equals(EApplBizDv.KICS) ? 
                 irSprdShkMap.getOrDefault(spot.getMatCd(), 0.0) : 0.0;
double lpDisc       = irSprdLpMap.getOrDefault(spot.getMatCd(), 0.0);
double spotCont     = baseSpotCont + shkCont;
double spotDisc     = irContToDisc(spotCont);
double adjSpotDisc  = spotDisc + lpDisc;
double adjSpotCont  = irDiscToCont(adjSpotDisc);	
        
//260
// double baseSpotCont = irDiscToCont(baseSpot);

// double shkCont 
// = (applBizDv.equals("KICS")&&swSce.getKey()<= kicsAddSprdContSceNo) ?
//    irSprdShkMap.getOrDefault(spot.getMatCd(), 0.0) + addSprd 
//  : irSprdShkMap.getOrDefault(spot.getMatCd(), 0.0);
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



## 1. curveMap

결정론적 시나리오 산출 대상을 금리커브 및 금리시나리오 (irCurveSceNo) 에 따라 구분함. (반복작업) &#x20;

<details>

<summary><code>IrCurveNm : ParamSw</code></summary>

&#x20;작업대상 입력모수 : 동일한 금리커브라도 설정한 시나리오 갯수만큼 작업을 반복함.

{% code overflow="wrap" %}
```java
Map.Entry<String, Map<Integer, IrParamSw>> 
    curveSwMap : paramSwMap.entrySet()
```
{% endcode %}

*

    <figure><img src="../../../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

</details>

## 2. ytmList&#x20;

금리 커브 별 ytm 정보 입수.&#x20;

<details>

<summary><code>List ytmList</code> </summary>

```java
List ytmList = IrCurveYtmDao.getIrCurveYtm(bssd, curveSwMap.getKey())
```

*

    <figure><img src="../../../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

</details>

## 3. 이하 작업은 시나리오별 loop&#x20;

<details>

<summary>3-1. <code>ytmAddList</code></summary>

* &#x20;ytm 가공(addSpread)하여 읽어오기
* &#x20;QIS 10.0 YTM 값에 직접 충격치를 반영하는 경우 처리를 위해, ytm에 스프레드를 가산한 ytmAddList 추가 &#x20;

{% code overflow="wrap" %}
```java
List ytmAddList = ytmList.stream()
    .map(s->s.addSpread(swSce.getValue().getYtmSpread()))
    .collect(Collectors.toList());
```
{% endcode %}

*   `addSpread`&#x20;

    <figure><img src="../../../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>
*

    <figure><img src="../../../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

</details>

<details>

<summary>3-2. <code>spotList</code></summary>

&#x20;읽어온 ytm을 이용하여 spot rate산출&#x20;

{% code overflow="wrap" %}
```java
List<IrCurveSpot> spotList 
= Esg150_YtmToSpotSw.createIrCurveSpot
    ( bssd
    , curveSwMap.getKey()
    , ytmAddList
    , swSce.getValue().getSwAlphaYtm()
    , swSce.getValue().getFreq())
    .stream().map(s-> s.convertToCont())
    .collect(Collectors.toList());
```
{% endcode %}

*

    <figure><img src="../../../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

&#x20;

</details>

<details>

<summary>3-3. <code>spotMap</code></summary>

{% code overflow="wrap" %}
```java
TreeMap<String, Double> spotMap 
= spotList.stream().collect(Collectors.toMap
    (IrCurveSpot::getMatCd, IrCurveSpot::getSpotRate
    , (k, v) -> k, TreeMap::new));
```
{% endcode %}

*

    <figure><img src="../../../../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

</details>

<details>

<summary>3-4. <code>irSprdLpMap</code></summary>

{% code overflow="wrap" %}
```java
Map<String, Double> irSprdLpMap 
= IrSprdDao.getIrSprdLpBizList
    (bssd, applBizDv, curveSwMap.getKey(), swSce.getKey())
    .stream().collect(Collectors.toMap
    (IrSprdLpBiz::getMatCd, IrSprdLpBiz::getLiqPrem));
```
{% endcode %}

*

    <figure><img src="../../../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

</details>

<details>

<summary>3-5. <code>irSprdShkMap</code></summary>

{% code overflow="wrap" %}
```java
Map<String, Double> irSprdShkMap 
= IrSprdDao.getIrSprdAfnsBizList
    ( bssd
    , irModelId
    , curveSwMap.getKey()
    , StringUtil.objectToPrimitive(swSce.getValue().getShkSprdSceNo(), 1)
    )
    .stream().collect(Collectors.toMap
    (IrSprdAfnsBiz::getMatCd, IrSprdAfnsBiz::getShkSprdCont));
```
{% endcode %}

*

    <figure><img src="../../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

</details>

<details>

<summary>3-6. <code>spotSceList</code></summary>

* 시나리오 결과를 저장하기 위해 deepCopy&#x20;
  * 시나리오 별로 반복 시, spot rate값을 직접 변경하면 다음 작업 시 영향을 받으므로 deepCopy 로 원본과 값 분리. &#x20;

{% code overflow="wrap" %}
```java
List spotSceList 
    = spotList.stream().map
    (s -> s.deepCopy(s)).collect(Collectors.toList());
```
{% endcode %}

*

    <figure><img src="../../../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

</details>

## 3.3\~3.5  스프레드 적용 순서 &#x20;

* <mark style="color:red;">`shkCont`</mark>  : AFNS 충격 스프레드는 연속(continuous) 복리 기준의 충격치임 &#x20;
* <mark style="color:red;">`lpDisc`</mark> : 유동성프리미엄은 이산 (discrete) 기준의 스프레드임&#x20;

1. `spotCont = baseSpotCont +`` `<mark style="color:red;">`shkCont`</mark>` ``;` &#x20;
2. `spotDisc = irContToDisc(spotCont) ;`&#x20;
3. `adjSpotDisc =  spot_disc +`` `<mark style="color:red;">`lpDisc`</mark>` ``;`&#x20;
4. `adjSpotCont = irDiscToCont (adjSpotDisc) ;`



