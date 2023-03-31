---
description: Bottom-Up 할인율 생성
---

# Bottom-Up Discount Rate

기본 무위험 금리기간구조를 바탕으로 조정항목( 변동성 조정, 유동성프리미엄 )을 반영함.

K-ICS에서 금리위험을 평가하기 위한 금리충격시나리오 역시 동일하게 충격 시나리오를 반영한 기본 무위험 금리기간구조에 조정항목 (변동성 조정, 유동성 프리미엄 )을 반영하는 구조로 260, 270, 280 작업에 걸쳐 수행됨.

<table data-view="cards"><thead><tr><th></th><th></th><th align="right"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><em><strong>base tenor 할인율</strong></em> </td><td><code>IR_DCNT_RATE_BU</code></td><td align="right">260</td><td><a href="df-base-tenor.md">df-base-tenor.md</a></td></tr><tr><td><em><strong>전체 tenor 할인율</strong></em></td><td><code>IR_DCNT_RATE</code></td><td align="right">270</td><td><a href="df-full-tenor.md">df-full-tenor.md</a></td></tr><tr><td><em><strong>목적별 할인율</strong></em></td><td><code>IR_DCNT_RATE_BIZ</code></td><td align="right">280</td><td><a href="df-by-biz.md">df-by-biz.md</a></td></tr></tbody></table>

* 260 은 base tenor 단위로 조정항목 반영.&#x20;
* 270 은 smith-wilson 보간을 통해 조정 무위험 금리 기간구조 생성.
* 280 은 자산과 부채에 따라 각각 조정항목 구분하여 반영.  &#x20;
  * 자산 : 유동성프리미엄 (변동성조정) 반영하지 않음
  * 부채 : 유동성프리미엄 (변동성조정) 반영.
