---
description: >-
  LIQ PREM APPL DV (유동성프리미엄 구분코드)에 따라서 최종적으로 조정 무위험 금리기간구조에 적용할 조정항목 (변동성 조정,
  유동성 프리미엄) 정보 적재
---

# Apply LP (VA)✱

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>rSprdLp</td><td>IR_SPRD_LP</td></tr><tr><td>output</td><td>IrSprdLpBiz</td><td>IR_SPRD_LP_BIZ</td></tr></tbody></table>



## Work Detail&#x20;

* [job250.md](../../../../etc/java/src/job250.md "mention")
* `IR PARAM SW` 에 설정된 `LIQ PREM APPL DV` (유동성프리미엄 구분코드) 에 따라서 최종적으로 할인율 산출에 적용할 조정항목 (변동성조정, 유동성프리미엄)을 선택하여 `IR SPRD LP BIZ` 적재함.
  * default  : `LIQ_PREM_APPL_DV =1`  <=> BU1&#x20;
  * 사용자는 산출 목적에 따라 환경설정(IR\_PARAM\_SW)을 변경하여 적용할 대상(BU1, BU2, BU3)을 선택할 수 있음&#x20;
