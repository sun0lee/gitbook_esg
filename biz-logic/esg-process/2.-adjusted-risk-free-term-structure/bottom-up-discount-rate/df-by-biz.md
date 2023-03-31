---
description: 전 구간 월단위로 산출된 금리커브를 기초로 자산시나리오와 부채 시나리오에 따라 조정여부가 상이하므로 구분하여 적재.
---

# 목적별 할인율

## Preview

<figure><img src="../../../../.gitbook/assets/image (80).png" alt=""><figcaption><p>IR_DCNT_RATE</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (64).png" alt=""><figcaption><p>IR_DCNT_RATE_BIZ</p></figcaption></figure>

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td>IrDcntRate</td><td>IR_DCNT_RATE</td></tr><tr><td>output</td><td>IrDcntRateBiz</td><td>IR_DCNT_RATE_BIZ</td></tr></tbody></table>



## Work Detail [job280.md](../../../../etc/java/src/job280.md "mention")

자산의 경우 조정(유동성프리미엄 또는 변동성조정)을 반영하지 않은 spot rate, fwd rate 값을 가져온다.&#x20;

부채의 경우 조정후 무위험금리기간구조인 adj spot rate, adj fwd rate를 가져온다





