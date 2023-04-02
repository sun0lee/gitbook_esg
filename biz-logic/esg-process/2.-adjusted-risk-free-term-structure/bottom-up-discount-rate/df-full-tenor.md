---
description: >-
  Esg270_IrDcntRate.createIrDcntRate : 기본 테너별로 산출한 조정 무위험 금리기간구조를 smith-wilson
  방식을 적용해 전체 월별 만기구간에 대해 보간/보외
---

# 전체 기간별 할인율

## Preview

기본 테너 (3M,6M,9M,... 240M)별 SPOT RATE를 이용하여 조정 무위험 금리기간구조 (1M\~1440M) 보간/ 보외 &#x20;

<figure><img src="../../../../.gitbook/assets/image (42).png" alt=""><figcaption><p>IR_CURVE_SPOT</p></figcaption></figure>

&#x20; 기본 테너 (3M,6M,9M,... 240M)별 YTM을 이용하여 기본 무위험 금리기간구조 (1M\~1440M) 보간/ 보외&#x20;

<figure><img src="../../../../.gitbook/assets/image (83).png" alt=""><figcaption><p>IR_CURVE_YTM</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (80).png" alt=""><figcaption><p>IR_DCNT_RATE</p></figcaption></figure>

### 기본 무위험 금리기간구조&#x20;

* SPOT RATE, FWD RATE &#x20;
* class : `SmithWilsonKicsBts`
* <mark style="color:red;">**자산평가**</mark>를 위한 기본 무위험 금리기간구조&#x20;
* ytm 값을 그대로 가져와서 smith-wilson 보간함. _<mark style="color:red;">(YTM은 기본시나리오만 있음!!!)</mark>_
* LTFR : ytmList의 Y50 (마지막 tenor) 값&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrCurveYtm</td><td>IR_CURVE_YTM</td></tr><tr><td>output</td><td>IrDcntRate</td><td>IR_DCNT_RATE</td></tr></tbody></table>



### 조정 무위험 금리기간구조&#x20;

* ADJ SPOT RATE, ADJ FWD RATE
* class : `SmithWilsonKics`
* <mark style="color:blue;">**부채평가**</mark>를 위한 조정 무위험 금리기간구조&#x20;
* spot rate (continuous) 값을 가져와서 smith-wilson 보간함. _<mark style="color:blue;">(금리 충격수준별 spot rate 산출결과)</mark>_
* LTFR  : paramSw 입력값 (금감원 제공)&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrCurveSpot</td><td>IR_CURVE_SPOT</td></tr><tr><td>output</td><td>IrDcntRate</td><td>IR_DCNT_RATE</td></tr></tbody></table>



'기본 무위험 금리기간구조' 와 'base시나리오'가 둘 다 '기본(base)' 이라는 단어로 혼용될 소지가 있으므로 이하에서는 아래의 용어를 사용함. &#x20;

* 기본 무위험 금리기간구조 (LTFR, LP 반영 전) -> Asset할인율&#x20;
* 조정 무위험 금리기간구조 (LTFR, LP 반영 후) -> Liab할인율 &#x20;



## Work Detail [job270.md](../../../../etc/java/src/job270.md "mention")

irCurveNm(금리커브) 단위로 paramSw에 설정된 irCurveSceNo(시나리오)마다 아래의 작업을 반복한다.&#x20;

* K-ICS(applBizDv)의 경우 민감도 분석 목적에 따라 YTM에 직접 충격을 반영하는 경우를 처리하기 위해 작업을 구분하였음.&#x20;

1. 부채 할인율 처리&#x20;
2. \~4. 자산 할인율을 위한 처리&#x20;


