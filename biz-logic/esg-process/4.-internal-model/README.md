---
description: 내부모형 목적의 시나리오 생성
---

# 4. Internal Model

검토사항, TODO&#x20;

* 자산 할인율도 필요한가?
* DNS vs. AFNS 조정항 적용여부를 어떻게 반영할지 !!!&#x20;
* 초기모수 산출 추가 (epsilon, sigma)

관련 테이블

* IR\_PARAM\_MODEL : 모델 id 설정 AFNS\_IM (internal model) / DNS 방식이냐에 따라 ENUM 수정필요.&#x20;
* IR\_CURVE\_SPOT : 금리내역 참고&#x20;

관련 job

<table data-view="cards"><thead><tr><th></th><th></th><th></th></tr></thead><tbody><tr><td><em><strong>모수 추정</strong></em></td><td>초기모수 산출 &#x26; 모수 최적화 </td><td>710,720</td></tr><tr><td><em><strong>스프레드 산출</strong></em> </td><td>충격 스프레드 산출 (det, sto)</td><td>730, 740</td></tr><tr><td><em><strong>할인율 금리커브 생성</strong></em></td><td></td><td>760,770</td></tr></tbody></table>

* 710,720 모수 추정 : 초기모수 산출 & 모수 최적화&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrParamModel, IrCurveSpot, IrCurveSpotWeek</td><td>IR_PARAM_MODEL, IR_CURVE_SPOT, IR_CURVE_SPOT_WEEK</td></tr><tr><td>output</td><td>IrParamAfnsCalc</td><td>IR_PARAM_AFNS_CALC</td></tr></tbody></table>

* 730, 740 충격 스프레드 산출결과

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrParamModel, IrCurveSpot, IrCurveSpotWeek</td><td>IR_PARAM_MODEL, IR_CURVE_SPOT, IR_CURVE_SPOT_WEEK</td></tr><tr><td>output</td><td>IrSprdAfnsCalc</td><td>IR_SPRD_AFNS_CALC</td></tr></tbody></table>

* 760 기준테너 할인율 생성

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td></td><td></td></tr><tr><td>output</td><td>IrDcntRateBuIm</td><td>IR_DCNT_RATE_BU_IM</td></tr></tbody></table>

* 770 full 테너 할인율 생성

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrDcntRateBuIm</td><td>IR_DCNT_RATE_BU_IM</td></tr><tr><td>output</td><td>IrDcntRateIm</td><td>IR_DCNT_SCE_IM</td></tr></tbody></table>



