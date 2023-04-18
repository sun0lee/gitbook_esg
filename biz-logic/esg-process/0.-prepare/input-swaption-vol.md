---
description: 스왑션변동성 입수 ; 금리커브 단위로 스왑션 만기별 테너별 변동성 정보를 가져와 unpivot 처리.
---

# input Swaption Vol

## Check

* 확률론적 금리시나리오 모수 추정시 사용함.&#x20;

## Preview

* 컬럼으로 구분된 테너별 스왑션 변동성을 row로 구분할 수 있도록 unpivot 처리함
* 단위변환 (% -> real number) &#x20;

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

<details>

<summary>이자율 스왑션 </summary>

#### 테너 vs.  만기

테너는 이자율 스왑션에서 유동금리 인덱스의 기간을 말하며, 일반적으로 1개월, 3개월, 6개월 등으로 표시됩니다. 예를 들어, 3개월 테너의 이자율 스왑션은 3개월마다 이자율을 지급하도록 계약된 금융상품을 말합니다. (금리를 교환하는 주기)

반면에 만기는 이자율 스왑션에서 계약이 종료되는 시점을 의미합니다. 예를 들어, 만기가 1년인 이자율 스왑션은 계약 시작일로부터 1년 후에 종료됩니다.

#### 이자율 스왑션 거래의 예시&#x20;

둘 다 만기가 1년이지만, 테너가 1개월인 이자율 스왑션과 테너가 3개월인 이자율 스왑션은 거래 방식과 리스크가 상이합니다.

테너가 1개월인 이자율 스왑션은 매월 정기적으로 이자율을 교환합니다. 예를 들어, A는 고정금리 4%로 1년 이자율 스왑을 체결하고 B는 1년 기간 동안 매월 변동하는 금리와 교환을 약속합니다. 만약 1개월 Euribor 금리가 3%인 경우, B는 A에게 (3%-고정금리 4%)×1/12= -0.08%의 이자를 지급합니다. 반면에, Euribor 금리가 5%인 경우, B는 (5%-4%)×1/12= 0.08%의 이자를 받습니다. 이런 식으로 매월 이자교환이 이루어지고, 1년 후에는 만기가 됩니다.

반면에, 테너가 3개월인 이자율 스왑션은 매월이 아니라 3개월마다 이자율을 교환합니다. 이 경우, 매월마다 교환하는 것보다는 변동성이 적으므로, 일반적으로 고정금리와 비교해서 스왑금리가 높게 형성됩니다. 그리고 만약 3개월 Euribor 금리가 상승한다면, 3개월 이자율 스왑션의 위험도 높아지므로 고정금리로 1년 이자율 스왑을 체결하는 것이 보다 안정적인 선택일 수 있습니다.

</details>

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrVolSwpnUsr</td><td>IR_VOL_SWPN_USR</td></tr><tr><td>output</td><td>IrVolSwpn</td><td>IR_VOL_SWPN</td></tr></tbody></table>

## Work Detail

* [job120.md](../../../etc/java/src/job120.md "mention")
* IR\_VOL\_SWPN\_USR -> IR\_VOL\_SWPN

