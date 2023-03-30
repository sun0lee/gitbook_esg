---
description: Smith-wilson 보간법을 이용하여 ytm을 spot rate로 변환. (base tenor기준)
---

# Spot Rate 변환 (150)

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrCurveYtm</td><td>IR_CURVE_YTM</td></tr><tr><td>output</td><td>IrCurveSpot</td><td>IR_CURVE_SPOT</td></tr></tbody></table>

## Work Detail([Broken link](broken-reference "mention"))

* 시장에서 관측되는 base tenor 단위별 YTM 정보를 이용하여 s-w 방법론을 적용하여 spot rate(cont)로 변환함.&#x20;
* IR\_CURVE\_YTM -> IR\_CURVE\_SPOT
* 이때 이자지급주기(1Y)는 2회를 가정하며
* 수렴속도 $$\alpha=0.1$$을 적용한다.&#x20;

<details>

<summary>FSS_IFRS17 및 K-ICS 금리기간구조(원화)_'22.12말.xlsm</summary>

* YTM을 spot rate로 변환할 때에는 수렴속도 alpha = 0.1을 적용한다.&#x20;
* 매년 이자지급주기는 2회&#x20;

![](<../../../.gitbook/assets/image (19).png>)

</details>

## 0. 금리커브 단위로 반복&#x20;

금리커브 사용여부 Y 인 금리커브 단위로 loop&#x20;

```java
for(String irCurveNm : irCurveNmList)
```

{

## 1. irParamSw 설정 check&#x20;

{% code overflow="wrap" %}
```java
// IR_PARAM_SW 설정여부 확인
if(!irCurveSwMap.containsKey(irCurveNm)) {
log.warn("No Ir Curve Data [{}] in Smith-Wilson Map for [{}]", irCurveNm, bssd);
continue;
}
```
{% endcode %}

## 2. delete

```java
// 기준일자의 이전 작업 결과 delete 
IrCurveSpotDao.deleteIrCurveSpotMonth(bssd, irCurveNm);
```

## 3. input ; YTM&#x20;

```java
List<IrCurveYtm> ytmRstList = 
IrCurveYtmDao.getIrCurveYtmMonth(bssd, irCurveNm);	
```

## 4. input check ; YTM 건수&#x20;

```java
// input ytm 적재여부 확인 
if(ytmRstList.size()==0) {
  log.warn("No Historical YTM Data exist for [{}, {}]", bssd, irCurveNm);
  continue;
}				
```

## 5. YTM -> Spot

### 5.1. treeMap 기준일자별 YTM

{% code overflow="wrap" %}
```java
// (이미 irCurve 기준으로 loop돌고 있음) 
// TreeMap : 기준일자별, (만기별 ytm) 생성
TreeMap<String, List<IrCurveYtm>> ytmRstMap 
    = new TreeMap<String, List<IrCurveYtm>>();
ytmRstMap = ytmRstList.stream()
 .collect(Collectors.groupingBy
       ( s -> s.getBaseDate()
       , TreeMap::new
       , Collectors.toList()
       ) 
 );					
```
{% endcode %}

### 5.2 날짜별 loop &#x20;

{% code overflow="wrap" %}
```java
// 생성한 ytm 트리맵 기준일자별로 루프 
for(Map.Entry<String, List<IrCurveYtm>> ytmRst : ytmRstMap.entrySet()) {
```
{% endcode %}



* **5.2.1. List rst spotRate**

```java
// spot rate 만들어서 담을 통 
List<IrCurveSpot> rst = new ArrayList<IrCurveSpot>();
```



* **5.2.2. ytmToSpotSw**&#x20;
  * ****[with-assets-generating-multiple-cf](../../undefined/smith-wilson-method/with-assets-generating-multiple-cf/ "mention") 에서 biz logic 검토&#x20;

```java
// biz로직 :ytm -> spot 
rst = Esg150_YtmToSpotSw
 .createIrCurveSpot
  ( ytmRst.getKey()
  , irCurveNm
  , ytmRst.getValue()
  , irCurveSwMap.get(irCurveNm).getSwAlphaYtm()
  , irCurveSwMap.get(irCurveNm).getFreq()
  );
```



* **5.2.3. fk setter**&#x20;

```java
// ir curve에 대한 정보 추가 setter (fk)
rst.forEach(s -> s.setIrCurve(irCurveMap.get(irCurveNm)));
```

* **5.2.4. exception**&#x20;

```java
// 결과값이 비어있으면 예외 던지기 
if(rst.isEmpty()) throw new Exception();
```

* **5.2.5. save (저장쿼리를 생성.)**

```java
rst.forEach(s -> session.save(s));
```

### 5.3.날짜별 DB commit

```java
session.flush();
session.clear();
```

}

## 99.종료 log&#x20;

금리커브 별로 모든 작업이 성공해야 성공 로그 출력.

```java
completeJob("SUCCESS", jobLog);
```
