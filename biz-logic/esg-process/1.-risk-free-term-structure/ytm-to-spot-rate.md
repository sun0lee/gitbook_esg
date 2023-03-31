---
description: Smith-wilson 보간법을 이용하여 ytm을 spot rate로 변환. (base tenor기준)
---

# Spot Rate 변환

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrCurveYtm</td><td>IR_CURVE_YTM</td></tr><tr><td>output</td><td>IrCurveSpot</td><td>IR_CURVE_SPOT</td></tr></tbody></table>

## Work Detail [job150.md](../../../etc/java/src/job150.md "mention")

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
