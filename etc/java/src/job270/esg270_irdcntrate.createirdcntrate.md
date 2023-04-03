# Esg270\_IrDcntRate.createIrDcntRate()

### 1. Common &  for all Scen

<details>

<summary>baseScen 통 만들기 => for Asset 할인율</summary>

```java
Map<String, IrDcntRate> adjRateSce1Map       = new TreeMap<String, IrDcntRate>();
Map<String, SmithWilsonRslt> baseRateSce1Map = new TreeMap<String, SmithWilsonRslt>(); 
```

#### &#x20;Asset 할인율 (기본 무위험 금리기간구조)에 충격시나리오 반영방안&#x20;

대상

* K-ICS표준모형 : 금리위험 산출을 위해 결정론적 금리 충격시나리오를 반영함.&#x20;

문제점&#x20;

* Asset 할인율은 ytm 기반 보간 ->금리 shock 수준별 결과가 없음 (only base 시나리오)&#x20;
* Liab 할인율은  spot rate 기반 보간 -> 금리 shock 수준별 결과가 있음
* 결정론적 금리shock은 ytm에 직접 주지 않고, spot rate로 변환 후 충격을 주는 구조이기 때문.&#x20;

해결방법

* Asset 할인율에 충격수준 반영을 위해서 만기별로 충격스프레드(충격 전후의 Liab 할인율의 차이)를 산출해서 Asset 할인율 (base시나리오)에 가산하는 방식으로 산출함. (감독원 엑셀)
* 이 처리를 위해 baseScenRst 결과를 따로 집계함
  * &#x20;`adjRateSce1Map, baseRateSce1Map`

</details>

<details>

<summary>Liab 할인율 smith-wilson 변환 (<a data-mention href="../../../../biz-logic/interest-rate-model/smith-wilson-method/with-zero-coupon-bonds/smithwilsonkics.class.md">smithwilsonkics.class.md</a>)</summary>

```java
SmithWilsonKics swKics = new SmithWilsonKics
 ( baseDate
 , irCurveSpotList
 , CMPD_MTD_DISC
 , true
 , swSce.getValue().getLtfr()
 , swSce.getValue().getLtfrCp()
 , projectionYear, 1, 100, DCB_MON_DIF);
```

</details>

<details>

<summary>[<code>adjRateList</code>] Liab 할인율 -> save </summary>

```java
// DCNT 결과담기 (조정;Liab할인율)
List<IrDcntRate> adjRateList 
= swKics.getSmithWilsonResultList()
  .stream().map(s -> s.convert()).collect(Collectors.toList());
```

* 아직 부채할인율만 담아서 자산할인율항목(spotRate, fwdRate)은 null임&#x20;

<img src="../../../../.gitbook/assets/image (41).png" alt="" data-size="original">

</details>

<details>

<summary>[<code>adjRateMap</code>]  L scen</summary>

만기코드 단위로 충격스프레드 산출하기 위해 map 생성

```java
Map<String, IrDcntRate> adjRateMap 
= adjRateList.stream().collect(Collectors.toMap
 (IrDcntRate::getMatCd, Function.identity()
 , (k, v) -> k
 , TreeMap::new));				

TreeSet<Double> tenorList 
= adjRateList.stream().map
  (s -> Double.valueOf(1.0 * Integer.valueOf(s.getMatCd().substring(1)) / MONTH_IN_YEAR))
  .collect(Collectors.toCollection(TreeSet::new)); 
```

<img src="../../../../.gitbook/assets/image (78).png" alt="" data-size="original">

</details>

### 2. K-ICS &  only if Scen #1

<details>

<summary>[<code>adjRateSce1Map</code>]  L base</summary>

```java
adjRateSce1Map 
= adjRateList.stream()
 .collect(Collectors.toMap
 (IrDcntRate::getMatCd, Function.identity()
 , (k, v) -> k
 , TreeMap::new
 ));
```

* 위에서 이미 부채할인율은 smith-wilton 보간했음. 그 결과 중에 시나리오 1번은 1번 통에 담기 &#x20;

<img src="../../../../.gitbook/assets/image.png" alt="" data-size="original">

</details>

<details>

<summary>Asset 할인율 smith-wilson 변환</summary>

```java
List<IrCurveYtm> ytmList 
 = IrCurveYtmDao
  .getIrCurveYtm(bssd, curveSwMap.getKey());	

SmithWilsonKicsBts swBts 
 = SmithWilsonKicsBts.of()
    .baseDate(baseDate)					
    .ytmCurveHisList(ytmList)
    .alphaApplied(StringUtil.objectToPrimitive(swSce.getValue().getSwAlphaYtm(), 0.1))													 
    .freq(StringUtil.objectToPrimitive(swSce.getValue().getFreq(), 2))
    .build();

```

</details>

<details>

<summary>[<code>baseRateSce1Map</code>]  A base</summary>

```java
baseRateSce1Map 
 = swBts.getSmithWilsonResultList(prjTenor).stream()
	.collect(Collectors.toMap
	(SmithWilsonRslt::getMatCd, Function.identity()));
```

<img src="../../../../.gitbook/assets/image (81).png" alt="" data-size="original">

</details>

<details>

<summary>[<code>adjRateList</code>] Asset 할인율 -> save </summary>

```java
// DCNT 결과담기 (기본)  
for(IrDcntRate rslt : adjRateList) {							
    rslt.setSpotRate(baseRateSce1Map.get(rslt.getMatCd()).getSpotDisc());
    rslt.setFwdRate (baseRateSce1Map.get(rslt.getMatCd()).getFwdDisc());}
```

<img src="../../../../.gitbook/assets/image (31).png" alt="" data-size="original">

<img src="../../../../.gitbook/assets/image (30).png" alt="" data-size="original">

</details>

### 3. K-ICS &  only if Scen #2\~#5

<details>

<summary>A scen  =  A base + ( L scen - L base )  =>  L scen + (A base - L base)</summary>

* 엑셀로직  A base + ( L scen - L base ) &#x20;
  * 시나리오별 자산할인율 = 자산 기준시나리오 값에 만기별 금리충격수준 반영
* 엔진로직 . L scen + (A base - L base)
  * 시나리오별 자산 할인율 = 시나리오별 부채할인율에 만기별 자산/부채 스프레드조정 반영

```java
TreeMap<String, Double> spotRateMap = new TreeMap<String, Double>();
TreeMap<String, Double> fwdRateMap  = new TreeMap<String, Double>();

for(IrDcntRate rslt : adjRateList) {						
   String matCd   = rslt.getMatCd();						
   double adjRate = adjRateMap.get(matCd).getAdjSpotRate();
   double adjDiff = baseRateSce1Map.get(matCd).getSpotDisc(). 
         	   - adjRateSce1Map.get(matCd).getAdjSpotRate();
	
	rslt.setSpotRate(adjRate + adjDiff);						
	spotRateMap.put(matCd, adjRate + adjDiff);
}					
fwdRateMap = irSpotDiscToFwdM1Map(spotRateMap);					

for(IrDcntRate rslt : adjRateList) {
	rslt.setFwdRate(fwdRateMap.get(rslt.getMatCd()).doubleValue());
}		
```

</details>

### 4.  K-ICS 외&#x20;

<details>

<summary>[<code>adjRateSce1Map</code>]  L base</summary>

```java
adjRateSce1Map = adjRateList.stream()
.collect(Collectors.toMap(IrDcntRate::getMatCd, Function.identity()
                        , (k, v) -> k, TreeMap::new));
```

</details>

<details>

<summary>Asset 할인율 smith-wilson 변환</summary>

```java
List<IrCurveYtm> ytmList 
= IrDcntRateDao
 .getIrDcntRateBuToBaseSpotList(bssd, applBizDv, curveSwMap.getKey(), swSce.getKey())
 .stream().map(s -> s.convertSimpleYtm()).collect(Collectors.toList());					
//자산 할인율 
SmithWilsonKicsBts swBts 
= SmithWilsonKicsBts.of()
	 .baseDate(baseDate)					
	 .ytmCurveHisList(ytmList)
	 .alphaApplied(StringUtil.objectToPrimitive(swSce.getValue().getSwAlphaYtm(), 0.1))													 
	 .freq(0)
	 .build();								
```

</details>

<details>

<summary>[<code>baseRateSce1Map</code>]  A base</summary>

```java
baseRateSce1Map 
= swBts.getSmithWilsonResultList(prjTenor).stream()
 .collect(Collectors.toMap
 (SmithWilsonRslt::getMatCd, Function.identity()));		
```

</details>

<details>

<summary>[<code>adjRateList</code>] Asset 할인율 -> save</summary>

```java
for(IrDcntRate rslt : adjRateList) {						
 rslt.setSpotRate(baseRateSce1Map.get(rslt.getMatCd()).getSpotDisc());
 rslt.setFwdRate (baseRateSce1Map.get(rslt.getMatCd()).getFwdDisc());
}		
```

</details>





