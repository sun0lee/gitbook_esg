---
description: >-
  기본 무위험금리기간구조 산출에 기초가 되는 채권의 시가평가 기준 수익률(YTM : yield to Maturity) 정보를 입수함. 두
  가지의 입력방식이 존재
---

# input YTM

## Source

* [https://www.kofiabond.or.kr](https://www.kofiabond.or.kr) (금융투자협회 채권정보센터)=> 시가평가 => 채권시가평가기준수익률 &#x20;

<figure><img src="../../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

## Table&#x20;

#### \[input 01]&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrCurveYtmUsrHis</td><td>IR_CURVE_YTM_USR_HIS</td></tr><tr><td>output</td><td>IrCurveYtm</td><td>IR_CURVE_YTM</td></tr></tbody></table>

* KOFIA BOND 와 동일한 layout, 만기를 컬럼으로 구분함. 입력단위 (% ; toReal 0.01) &#x20;
* 3% 인경우 3을 입력하는 방식&#x20;



#### \[input 02]&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrCurveYtmUsr</td><td>IR_CURVE_YTM_USR</td></tr><tr><td>output</td><td>IrCurveYtm</td><td>IR_CURVE_YTM</td></tr></tbody></table>

* 만기구분을 코드값으로 구분함. 입력단위 (number ; toReal 1)&#x20;
* 3%인 경우 0.03을 입력하는 방식&#x20;



## Work Detail&#x20;

* [job130.md](../../../etc/java/src/job130.md "mention")
* 기준일자에는 아래의 두 작업 중 1가지 방식을 선택해서 작업함. 즉, 사용자는 두 개의 USR 테이블 중 1개에만 YTM을 적재하면 됨. &#x20;
  1. IR\_CURVE\_YTM\_USR\_HIS -> IR\_CURVE\_YTM
     * input 01 형태로 가져오는 경우 만기 구분이 컬럼으로 구분되기 때문에 unpivot 처리&#x20;
  2. IR\_CURVE\_YTM\_USR -> IR\_CURVE\_YTM
     * input 02 형태로 가져오는 경우 1:1 move  (과거 데이터 일괄 적재용인가 ?)&#x20;

1.

