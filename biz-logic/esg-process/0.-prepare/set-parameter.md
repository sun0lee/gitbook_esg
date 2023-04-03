---
description: 업무구분 (IFRS, KICS, IBIZ, SAAS)에 따라 필요한 입력변수를 Map으로 구분함. 금리커브 및 시나리오 단위로 설정함.
---

# set Parameter✱

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrParamSwUsr</td><td>IR_PARAM_SW_USR</td></tr><tr><td>output</td><td>rParamSw</td><td>IR_PARAM_SW</td></tr></tbody></table>

IR\_PARAM\_SW\_USR에 사용자가 엔진 수행시 필요한 입력변수(parameter)를 설정한다. 엔진에서 사용자 설정 데이터를 직접 읽는 경우, 작업 이후에 사용자가 입력변수를 수정하면 산출당시에 사용된 입력변수 추적이 어려워진다.&#x20;

이를 방지하기 위해 사용자가 입력한 환경 설정데이터를 기준으로  엔진에서 산출시점에 사용하는 입력변수를 IR\_PARAM\_SW 에 적재한다. 이는 산출에 대한 환경변수 이력관리를 통해 산출시점에 적용된 모수 추적을 용이하게 하기 위함임.&#x20;



## Work Detail

* [job110.md](../../../etc/java/src/job110.md "mention")

1. IR\_PARAM\_SW\_USR -> IR\_PARAM\_SW
2. 엔진 내부적으로는 업무구분 (IFRS, KICS, IBIZ, SAAS)에 따라 이후 작업에 필요한 입력변수를 Map으로 구분함. applBizDvSet : 이후 작업에서 biz구분에 따라 각각의 맵을 이용하여 작업하기 때문에 110번 작업을 필수작업이 됨.&#x20;
   * BIZ 구분 : kicsSwMap / ifrsSwMap / ibizSwMap / saasSwMap
   * 금리커브  : irCurveNmSet
   * 시나리오  : irCurveSceNoSet

