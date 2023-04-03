---
description: 기본 무위험 금리기간구조에 조정항목을 반영하여 조정 무위험금리기간구조 산출함. 금리위험 산출용 금리충격시나리오 반영하는 것도 여기서 처리함.
---

# DF (base Tenor)

## Check

* 261 : spot rate는 왜 전에 적재한거 안쓰고 다시 ytm을 변환해서 읽어오는지 확인하기
  * 260 : ytm -> <mark style="color:red;">spot + shock</mark> -> smith-wilson mth&#x20;
  * 261 : <mark style="color:red;">ytm + spread</mark> -> spot  -> smith-wilson mth  &#x20;

<details>

<summary>job261 수정 이유 </summary>

&#x20;QIS10\_0 : 100bp up/down -> ytm에 직접 반영하는 경우를 처리하기 위해 수정함. &#x20;

* 금리 충격은 신용위험요소말고 "금리" 자체에만 위험효과를 반영하기 위해 무위험에 가산하라고 해놓고 왜 ytm에 직접 치도록 했을까 ??? (궁금) &#x20;
*

    <figure><img src="../../../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

</details>

<img src="../../../../.gitbook/assets/file.excalidraw (1).svg" alt="" class="gitbook-drawing">

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td></td><td></td></tr><tr><td>output</td><td>IrDcntRateBu</td><td>IR_DCNT_RATE_BU</td></tr></tbody></table>

## Work Detail

* &#x20;[job260](../../../../etc/java/src/job260/ "mention")
* bottom-up 방식에 따라 기본 무위험 금리기간구조에 조정항목(유동성프리미엄, 변동성 조정)을 반영하여 조정 무위험기간구조를 산출함.&#x20;
* 결정론적 금리 충격 시나리오를 적용하는 경우 , paramSw설정에 따라 시나리오를 구분하여 충격시나리오를 반영한 결과를 산출함. (조정항목 + 충격시나리오 반영)

