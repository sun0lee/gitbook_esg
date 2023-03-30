---
description: 기본 무위험 금리기간구조에 조정항목을 반영하여 조정 무위험금리기간구조 산출함. 금리위험 산출용 금리충격시나리오 반영하는 것도 여기서 처리함.
---

# base Tenor별 할인율 (260)

## Check

* 261 : spot rate는 왜 전에 적재한거 안쓰고 다시 ytm을 변환해서 읽어오는지 확인하기
  * 260 : ytm -> <mark style="color:red;">spot + shock</mark> -> smith-wilson mth&#x20;
  * 261 : <mark style="color:red;">ytm + spread</mark> -> spot  -> smith-wilson mth  &#x20;

<details>

<summary>job261 수정 이유 </summary>

&#x20;QIS10\_0 : 100bp up/down -> ytm에 직접 반영하는 경우를 처리하기 위해 수정함. &#x20;

* 금리 충격은 신용위험요소말고 "금리" 자체에만 위험효과를 반영하기 위해 무위험에 가산하라고 해놓고 왜 ytm에 직접 치도록 했을까 ??? (궁금) &#x20;
*

    <figure><img src="../../../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

</details>

<img src="../../../../.gitbook/assets/file.excalidraw (1).svg" alt="" class="gitbook-drawing">

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td></td><td></td></tr><tr><td>output</td><td>IrDcntRateBu</td><td>IR_DCNT_RATE_BU</td></tr></tbody></table>

## Work Detail

bottom-up 방식에 따라 기본 무위험 금리기간구조에 조정항목(유동성프리미엄, 변동성 조정)을 반영하여 조정 무위험기간구조를 산출함.&#x20;

결정론적 금리 충격 시나리오를 적용하는 경우 , paramSw설정에 따라 시나리오를 구분하여 충격시나리오를 반영한 결과를 산출함. (조정항목 + 충격시나리오 반영)



### &#x20;Esg261\_IrDcntRateBu\_Ytm.setIrDcntRateBu(bssd, "AFNS", "KICS", kicsSwMap)

#### 1. curveMap

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

    <figure><img src="../../../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

</details>

#### 2. ytmList&#x20;

금리 커브 별 ytm 정보 입수.&#x20;

<details>

<summary><code>List ytmList</code> </summary>

```java
List ytmList = IrCurveYtmDao.getIrCurveYtm(bssd, curveSwMap.getKey())
```

*

    <figure><img src="../../../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

</details>

#### 3. 이하 작업은 시나리오별 loop&#x20;

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

*   ``

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

*   ``

    <figure><img src="../../../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

</details>

#### 3.3\~3.5  스프레드 적용 순서 &#x20;

* <mark style="color:red;">`shkCont`</mark>  : AFNS 충격 스프레드는 연속(continuous) 복리 기준의 충격치임 &#x20;
* <mark style="color:red;">`lpDisc`</mark> : 유동성프리미엄은 이산 (discrete) 기준의 스프레드임&#x20;

1. `spotCont = baseSpotCont +`` `<mark style="color:red;">`shkCont`</mark>` ``;` &#x20;
2. `spotDisc = irContToDisc(spotCont) ;`&#x20;
3. `adjSpotDisc =  spot_disc +`` `<mark style="color:red;">`lpDisc`</mark>` ``;`&#x20;
4. `adjSpotCont = irDiscToCont (adjSpotDisc) ;`
