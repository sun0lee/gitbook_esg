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

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrVolSwpnUsr</td><td>IR_VOL_SWPN_USR</td></tr><tr><td>output</td><td>IrVolSwpn</td><td>IR_VOL_SWPN</td></tr></tbody></table>

## Work Detail( )

IR\_VOL\_SWPN\_USR -> IR\_VOL\_SWPN

