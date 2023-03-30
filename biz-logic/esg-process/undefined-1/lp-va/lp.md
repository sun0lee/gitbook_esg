---
description: >-
  금리기간구조의 조정항목(유동성프리미엄 or 변동성조정)은 적용 단위에 따라 3가지 방식(BU1, BU2, BU3)으로 적용할 수 있는 구조로
  설계되어 있으며, 각 구분코드별로 조정항목을 적재함
---

# LP 산출

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrParamSw.liqPrem</td><td>IR_PARAM_SW.LIQ_PREM</td></tr><tr><td>output</td><td>IrSprdLp</td><td>IR_SPRD_LP</td></tr></tbody></table>

유동성 프리미엄을 상수(constant)로 무위험 금리기간구조에 반영하는 형태가 default 임 .

&#x20;내부모형과 같이 목적에 따라 유동성 프리미엄(변동성 조정)의 수준을 만기에 따라 차등 적용하거나 금리커브의 종류에 따라 상이한 수준을 적용하고 싶은 경우 설정에서 제어할 수 있음. (Work Detail 참조)



## Work Detail [job240.md](../../../../etc/java/src/job240.md "mention")

조정항목 (변동성 조정, 유동성 프리미엄)은 요건에 따라 다양한 방식으로 산출할 수 있으며, 적용단위 또한 사용자의 목적에 따라 조정 가능한 항목임.&#x20;

할인율 산출에 최종 적용하는 조정항목 값은 이 작업에서 결정하지 않음. 환경설정에 따르며, 250 작업에서 판단함. &#x20;

{% tabs %}
{% tab title="BU1" %}
`setLpFromSwMap`

금감원에서 제공한 조정 값(변동성 조정, 유동성 프리미엄)처럼 만기 구분없이 일정한 상수값(constant)을 적용하고자 하는 경우.  변동성 조정 산출식의 인정비율(KICS 80%, IFRS 100%)이 이미 반영되어 산출된 상수를 조정항목 (변동성 조정, 유동성프리미엄) 값에 입력함.&#x20;



#### 참조 값 setting&#x20;

* Table : IR\_PARAM\_SW
* Column : LIQ\_PREM



#### 환경 설정&#x20;

* Table : IR\_PARAM\_SW
* Column : LIQ\_PREM\_APPL\_DV = 1 **(Default)**
{% endtab %}

{% tab title="BU2" %}
`setLpFromCrdSprd`&#x20;

만기에 따라 조정 수준을 차등 적용하고자하는 경우 IR\_SPRD\_CURVE 테이블의 만기별 유동성 프리미엄 값을 적용함.&#x20;



#### 참조 값 setting&#x20;

* Table : IR\_SPRD\_CURVE
* where LP\_CURVE\_ID ="5010110"&#x20;



#### 환경 설정&#x20;

* Table : IR\_PARAM\_SW
* Column : LIQ\_PREM\_APPL\_DV = 2&#x20;
{% endtab %}

{% tab title="BU3" %}
`setLpFromUsr`

금리커브 및 만기에  따라 조정항목(변동성 조정, 유동성 프리미엄)의 수준을 차등 적용하고 싶은 경우 적용.



#### 참조 값 setting&#x20;

* Table : IR\_SPRD\_LP\_USR&#x20;



#### 환경 설정&#x20;

* Table : IR\_PARAM\_SW
* Column : LIQ\_PREM\_APPL\_DV = 3&#x20;
{% endtab %}
{% endtabs %}

