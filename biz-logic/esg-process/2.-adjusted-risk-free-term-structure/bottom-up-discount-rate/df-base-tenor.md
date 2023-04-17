---
description: 기본 무위험 금리기간구조에 조정항목을 반영하여 조정 무위험금리기간구조 산출함. 금리위험 산출용 금리충격시나리오 반영하는 것도 여기서 처리함.
---

# DF (base Tenor)

## ToDo

### 지금 260에서 하는 일&#x20;

job 260 에서 너무 많은 작업을 하고 있음. 작업을 쪼갤 필요가 있음 !!!  정의한 결정론적 시나리오 별 base tenor 단위의 spot rate 생성하기&#x20;

* 기본 spotMap
* 조정 spotMap = 기본 spotMap + LP
* 조정 + 충격 spotMap = 기본 spotMap + LP + shock
* ( 기본 + 충격 spotMap = 기본 spotMap + shock )--> 270 에서 처리하고 있음.   &#x20;



### 유동성 프리미엄 반영여부에 따라&#x20;

260에서는 base Tenor 단위의 spotRate 세트를 만들고 270에서는 그게 뭐가 됐든 상관없이 보간/ 보외 처리만 할 수 있나 ?&#x20;

* 자산 : LP 미적용 / 부채 : LP 반영&#x20;

자산용인지 부채용인지 구분 ? (SW 방식이 다름. 정확히는&#x20;

* ytm을 직접 이용하여 보간할때 alpha : 0.1&#x20;
* spot 을 이용하여 보간할때 minimize하는 최적 alpha를 적용)&#x20;
* \=> 이 두 개의 결과를 같게 만드는 alpha를 어떻게 알 수 있지? 그때 그 때 시장상황과 그때의 ytm에 따라 달라지는 거라면 자산용 부채용은 구분해서 가는 수 밖에 없음 !!!&#x20;



### 충격 적용 방식에 따라&#x20;

base tenor 기준의 무위험 금리커브를 가져온다. (spot)

기본 시나리오 여부 ? (금리 충격 반영여부 ?)

* Y : spotMap -> 끝&#x20;
* N : 충격 시나리오 정의 (Det (AFNS ,User Define)) / 확률론 시나리오는 별도 작업에서 처리하므로 여기에서는 금리 충격을 반영하더라도 결정론 시나리오 단위로만 설정함.&#x20;
  1. shock 반영대상 : YTM ? spot ?
  2. shock 반영방식 정의 :&#x20;
     * AFNS : 관련 모수 설정 및 사전작업이랑 연계
     * User Define.&#x20;
       * 만기별 차등 shock을 줄거면 AFNS 결과 올린 테이블에 같이 올리면 반영 됨.&#x20;
       * 만기 구분없이&#x20;
         * 승산, 가산 spread 적용하는 경우 sw 에서 설정.&#x20;
         * 기존커브를 포워딩한 후에 승산, 가산 spread를 적용하는 경우, sw에서 설정.&#x20;





## Check

* 261 : spot rate는 왜 전에 적재한거 안쓰고 다시 ytm을 변환해서 읽어오는지 확인하기
  * 260 : ytm -> <mark style="color:red;">spot + shock</mark> -> smith-wilson mth&#x20;
  * 261 : <mark style="color:red;">ytm + spread</mark> -> spot  -> smith-wilson mth  &#x20;

<details>

<summary>job261 수정 이유 </summary>

&#x20;QIS10\_0 : 100bp up/down -> ytm에 직접 반영하는 경우를 처리하기 위해 수정함. &#x20;

* 금리 충격은 신용위험요소말고 "금리" 자체에만 위험효과를 반영하기 위해 무위험에 가산하라고 해놓고 왜 ytm에 직접 치도록 했을까 ??? (궁금) &#x20;
*

    <figure><img src="../../../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

</details>

<img src="../../../../.gitbook/assets/file.excalidraw (1).svg" alt="" class="gitbook-drawing">

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td></td><td></td></tr><tr><td>output</td><td>IrDcntRateBu</td><td>IR_DCNT_RATE_BU</td></tr></tbody></table>

## Work Detail

* &#x20;[job260](../../../../etc/java/src/job260/ "mention")
* bottom-up 방식에 따라 기본 무위험 금리기간구조에 조정항목(유동성프리미엄, 변동성 조정)을 반영하여 조정 무위험기간구조를 산출함.&#x20;
* 결정론적 금리 충격 시나리오를 적용하는 경우 , paramSw설정에 따라 시나리오를 구분하여 충격시나리오를 반영한 결과를 산출함. (조정항목 + 충격시나리오 반영)

