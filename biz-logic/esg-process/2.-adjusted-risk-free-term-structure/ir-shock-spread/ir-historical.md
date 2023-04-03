---
description: >-
  금리충격스프레드 생성을 위한 사전작업. IrCurveSpot 내역을 영업일 구분(한국 기준)하여 적재함. 회귀분석을 통한 AFNS 금리모형의
  모수 추정에 직전 10년치 무위험 기간구조 (spot rate) 를 사용함.
---

# IR (Historical)

iRateHisStBaseDate (2010-01-01)부터 작업일자까지 IR\_CURVE\_SPOT 내역 (history) 데이터를 기반으로 처리.  1000개 미만인 경우 통계량의 충분성 부족으로 산출 불가. (10년치의 데이터를 필요로 하고 있음)  &#x20;

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrCurveSpot</td><td>IR_CURVE_SPOT</td></tr><tr><td>output</td><td>IrCurveSpotWeek</td><td>IR_CURVE_SPOT_WEEK</td></tr></tbody></table>

## Work Detail

* &#x20;[job210](../../../../etc/java/src/job210/ "mention")
* IR\_CURVE\_SPOT -> IR\_CURVE\_SPOT\_WEEK&#x20;
* 기간 : 20100101(`iRateHisStBaseDate`)부터 \~기준일
