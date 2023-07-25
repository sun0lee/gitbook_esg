# Smith-Wilson Method

## 1. 방법론 개요

Smith-Wilson 방법론은 장기선도이자율(LTFR) 이 주어진 상태에서 시장이자율을 보간 및 보외하여 무위험할인율을 산출하는 방법. 금융당국의 향후 금리에 대한 전망을 LTFR에 설정하여 반영함.&#x20;

* LTFR에 금리가 수렴하는 속도계수인 $$\alpha$$를 산출해야 함.&#x20;



ESG에서는 아래의 작업에서 smith-wilson 방법론을 이용함.&#x20;

1. 시장의 ytm을  이용하여 spot rate로 변환할 때&#x20;
2. base tenor기준의 spot rate (continuous) 를 전체 만기에 대해 보간 보외 처리할 때&#x20;



ytm 을 input으로 하여 보간하거나 spot rate로 변환하는 경우 K-ICS기준서의 요건에 따라 이자지급횟수를 2번으로 가정하고 있으므로 with zero coupon bond 케이스가 아닌 multiple CF로 구성된 asset 기반의 smith-wilson 모형을 적용해야 함. => `SmithWilsonKicsBts`

반면에 spot rate (continuous)을 input으로 하여, 보간 보외 처리하는 경우, zero coupon bond 기반 smith-wilson 모형을 적용함. =>  `SmithWilsonKics`



<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><em><strong>wirh ZCB</strong></em></td><td><code>SmithWilsonKics</code></td><td>보간,보외 </td><td><a href="with-zero-coupon-bonds/">with-zero-coupon-bonds</a></td></tr><tr><td><em><strong>with Assets Multiple CFs</strong></em></td><td><code>SmithWilsonKicsBts</code></td><td>보간,보외 / 변환ytm2spot</td><td><a href="with-assets-generating-multiple-cf/">with-assets-generating-multiple-cf</a></td></tr></tbody></table>



참고자료&#x20;

{% file src="../../../.gitbook/assets/Extrapolation Methods of the Term Structure of Interest Rates under Solvency II.pdf" %}

