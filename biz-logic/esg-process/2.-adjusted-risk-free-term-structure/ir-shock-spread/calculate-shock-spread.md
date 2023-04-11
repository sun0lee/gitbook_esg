---
description: 금리충격스프레드 자체 생성
---

# Calculate Shock Spread

## Table

#### \[ 모수추정결과 ]&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrParamModel, IrCurveSpot, IrCurveSpotWeek</td><td>IR_PARAM_MODEL, IR_CURVE_SPOT, IR_CURVE_SPOT_WEEK</td></tr><tr><td>output</td><td>IrParamAfnsCalc</td><td>IR_PARAM_AFNS_CALC</td></tr></tbody></table>

<figure><img src="../../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

#### \[ 금리충격 스프레드 산출결과 ]&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td></td><td></td></tr><tr><td>output</td><td>IrParamAfnsCalc</td><td>IR_PARAM_AFNS_CALC</td></tr></tbody></table>

<figure><img src="../../../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

## Work Detail&#x20;

* [job220.md](../../../../etc/java/src/job220.md "mention")
* AFNS 모형 초기화
* AFNS 모수추정&#x20;
* 모형 파라메타 저장  `IR_PARAM_AFNS_CALC`
* 충격수준 산출결과 저장 `IR_PARAM_AFNS_CALC`

&#x20;
