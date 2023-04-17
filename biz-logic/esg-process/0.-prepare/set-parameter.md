---
description: 업무구분 (IFRS, KICS, IBIZ, SAAS)에 따라 필요한 입력변수를 Map으로 구분함. 금리커브 및 시나리오 단위로 설정함.
---

# set Parameter✱

## IrParamSw

ESG 프로세스 설정 마스터 격 담당. 여기에서 아래의 구분된 속성정보를 포괄해서 설정한다. (확률론 시나리오는 작업 자체가 구분되어 있어서 여기에선 제외)

즉 (확률론적 시나리오를 제외한) 생성해야 하는 금리 시나리오의 경우의 수

큰 그룹 속성에 따라 테이블을 구분해 버리면 /  혹은 지금처럼 한 테이블에서 설정하면 너무 많은 경우의 수가 발생할 수 있음. ( 곱사건으로 정의한다면 )-> 어떤 단위로 정리를 하든 이해를 하고 설정해야 함.  &#x20;

&#x20;

### 공통 정보&#x20;

* 기준일자, 적용 금리커브, sw 보간보외 (LLP, 최초수렴시점, LTFR, Alpha (적용방식은 다를 수 있음))



### 공통 외  &#x20;

| 시나리오구분 | sprd 항목                                                      | biz                                               | 시나리오 생성방식(금리모형)                                                   | sprd 적용대상/ 순서                                                                            |
| ------ | ------------------------------------------------------------ | ------------------------------------------------- | ----------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| base   | <ul><li>0 : 자산</li><li>LP(VA) : 부채 </li></ul>                | Comm                                              | n/a                                                               | n/a                                                                                      |
| Det.   | <ul><li>0 + shock : 자산</li><li>LP(VA) + shock : 부채</li></ul> | <ul><li>KICS</li><li>내부모형 (User-Define)</li></ul> | <ul><li>AFNS</li><li>승산/가산 스프레드</li><li>포워딩 + 승산/가산 스프레</li></ul> | <p></p><p>spot vs ytm</p><ul><li>spot (cont. vs disc.)</li><li>ytm (disc only)</li></ul> |
| Sto.   | <ul><li>LP(VA) : 부채만</li></ul>                               | <ul><li>IFRS 17</li><li>내부모형 </li></ul>           | <ul><li>HW1F</li><li>HW2F</li></ul>                               |                                                                                          |

* 금리모형에 따라 알아야 하는 것 , 정의해야 하는 변수가 상이함.&#x20;
* User Define은 누가 뭘 어느정도로 하고 싶냐의 따라 자유도를 어느정도 가져갈지&#x20;
* shock 및 유동성 프리미엄 등 금리커브 조정 스프레드를 반영할 금리의 대상 기준 (연속복리 vs 이산복리)
  * \-> 이거는 스프레드 산출방식에 따라 달라질 수 있을 것임. 금감원 모형은 자주바뀌지는 않겠지만 설정으로 바꿀 수 있는구조가 필요할까 ?
  * 테너별로 다르게 ? 아님 상수로 적용 ?&#x20;







## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrParamSwUsr</td><td>IR_PARAM_SW_USR</td></tr><tr><td>output</td><td>IrParamSw</td><td>IR_PARAM_SW</td></tr></tbody></table>

IR\_PARAM\_SW\_USR에 사용자가 엔진 수행시 필요한 입력변수(parameter)를 설정한다. 엔진에서 사용자 설정 데이터를 직접 읽는 경우, 작업 이후에 사용자가 입력변수를 수정하면 산출당시에 사용된 입력변수 추적이 어려워진다.&#x20;

이를 방지하기 위해 사용자가 입력한 환경 설정데이터를 기준으로  엔진에서 산출시점에 사용하는 입력변수를 IR\_PARAM\_SW 에 적재한다. 이는 산출에 대한 환경변수 이력관리를 통해 산출시점에 적용된 모수 추적을 용이하게 하기 위함임.&#x20;



## Work Detail

* [job110.md](../../../etc/java/src/job110.md "mention")

1. IR\_PARAM\_SW\_USR -> IR\_PARAM\_SW
2. 엔진 내부적으로는 업무구분 (IFRS, KICS, IBIZ, SAAS)에 따라 이후 작업에 필요한 입력변수를 Map으로 구분함. applBizDvSet : 이후 작업에서 biz구분에 따라 각각의 맵을 이용하여 작업하기 때문에 110번 작업을 필수작업이 됨.&#x20;
   * BIZ 구분 : kicsSwMap / ifrsSwMap / ibizSwMap / saasSwMap
   * 금리커브  : irCurveNmSet
   * 시나리오  : irCurveSceNoSet

