---
description: >-
  스프레드 적용 ; 결정론적 시나리오 생성에 적용할 테너별 충격수준을 IR_SPRD_AFNS_BIZ 에 생성함. 적용하고자 하는 원천에 따라
  두 가지 옵션이 있음.
---

# Apply Shock Spread✱

## Work Detail &#x20;

#### \[1순위] irSprdAfnsUsr가 값이 있으면 우선 적용 &#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrSprdAfnsUsr</td><td>IR_SPRD_AFNS_USR</td></tr><tr><td>output</td><td>IrSprdAfnsBiz</td><td>IR_SPRD_AFNS_BIZ</td></tr></tbody></table>

금감원에서 제공한 금리충격 스프레드 정보(Tenor별 상승 하락 수준)를 적용하여 결정론적 시나리오를 산출하고 싶은 경우 IR\_SPRD\_AFNS\_USR 테이블에 제공받은 충격수준을 입력한다.&#x20;

<figure><img src="../../../../.gitbook/assets/image (29) (1).png" alt=""><figcaption><p>금감원 제공 만기별 금리충격스프레드 예시 </p></figcaption></figure>





#### \[2순위] irShockCalc가 값이 있으면 적용 (이때 irSprdAfnsUsr는 없어야 함.)

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrSprdAfnsCalc</td><td>IR_SPRD_AFNS_CALC</td></tr><tr><td>output</td><td>IrSprdAfnsBiz</td><td>IR_SPRD_AFNS_BIZ</td></tr></tbody></table>

내부모형 등 산출목적에 따라 자체적으로 산출한 금리충격스프레드를 적용하고 싶은 경우 IR\_SPRD\_AFNS\_USR 에 해당월 정보를 지우고, 220 작업을 선행한다. IR\_SPRD\_AFNS\_CALC 에 충격수준이 생성되면 230 작업에서 이를 적용  테이블 (IR\_SPRD\_AFNS\_BIZ) 에 적재한다.&#x20;

## Work Detail

* &#x20;[job230.md](../../../../etc/java/src/job230.md "mention")

1.  IR\_SPRD\_AFNS\_USR 정보가 있는가 ?&#x20;

    * (yes) IR\_SPRD\_AFNS\_BIZ 에 해당 충격량 적재


2. (No) IR\_SPRD\_AFNS\_CALC (충격시나리오 산출결과)가 있는가 ?
   * (Yes) IR\_SPRD\_AFNS\_BIZ에 충격 시나리오 산출결과 적재&#x20;
   * (No) 종료&#x20;
