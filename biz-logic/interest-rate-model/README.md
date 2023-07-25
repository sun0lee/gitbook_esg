---
description: 금리모형
---

# Interest Rate Model

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><em><strong>Smith-Wilson Method</strong></em> </td><td>전체기간별 보간,보외 </td><td><a href="smith-wilson-method/">smith-wilson-method</a></td></tr><tr><td><em><strong>AFNS</strong></em> </td><td>결정론적 시나리오 생성 모델</td><td><a href="afns/">afns</a></td></tr><tr><td><em><strong>Hull-White 1 Factor mdl</strong></em></td><td>확률론적 시나리오 생성 모델 </td><td><a href="hull-white-1-factor.md">hull-white-1-factor.md</a></td></tr></tbody></table>

<details>

<summary>(참고) 이자율 모형</summary>

* **Equilibrium Models ; 균형모형** : 과거 데이터를 기반으로 시간의 경과에 따른 시장의 전반적인 형태를 관찰하여 미래에 실현 가능한 금리시나리오를 산출하는 방법임. Stress test, VaR 등 계산하기 위한 모형. Vasicek, CIR 모형 등&#x20;
  * **Vasicek (1977)** : $$dr(t) = \alpha [\theta - r(t)]dt +\sigma dz$$
    * $$r(t)$$는 정규분포를 따르며 음의 이자율이 발생하는 경우가 있음. -> Expected Vasicek&#x20;
    * 이자율 수준에 상관없이 변동성이 일정.
    * 모수는 회귀강도, 목표금리, 변동성 3가지로 구분됨.
    * 모수 추정은 closed form&#x20;
  * **CIR ; Cox, Ingersoll & Roll (1985) :** $$dr(t) = \alpha(\theta - r(t))dt  +sigma \sqrt{r(t)}dz, 2\alpha \sigma > \sigma^2$$
    * 위 모형에서 제약조건 $$2\alpha \sigma > \sigma^2$$을 만족시키면 항상 양의 이자율을 가지며, 금리가 증가하면 변동성도 증가함.&#x20;
    * 이자율은 Non-Central Chi-Squared 분포를 따르며, 모수는 수치적 방법을 통해 추정.&#x20;



* **No Arbitrage Models ; 무차익모형 :** 위험중립측도 기반, 현재의 시장정보(이자율 기간구조) 와 일치시켜 채권가격을 평가하는 모형. 시장에서 주어진 정보를 입력변수로 활용하여 모델을 통해 산출된 자산가격과 실제 시장에서 거래되는 자산의 가격이 일치하도록 설계함으써 장외파생상품 등 거래시장이 활발하지 않은 자산의 공정가격을 산출하기 위한 모형임. Hull-White , Black-Karasinski 등
  * &#x20;**Hull-White(1990)** : $$dr_t = \alpha (\theta(t)-r_t)dt + \sigma dZ$$
    * $$r(t)$$는 정규분포를 따르며, 음의 이자율이 발생할 수 있음.&#x20;
    * 이자율 수준에 상관없이 변동성이 일정
    * Vasicek 모형을 현재 수익률곡선에 적합시킬 수 있도록 수정한 모형&#x20;
  * **Black-Karasinski**  : $$dr_t = \alpha r_t (ln \theta(t) - ln r_t) dt + \sigma r_t dZ$$ ( $$dr_t = r_t(\eta_t - \alpha ln r_t)dt + \sigma r_t dZ$$ )
    * Hull-White 모형의 단점인 음의 이자율이 발생하지 않도록 이자율에 로그함수를 사용한 모형&#x20;
    * 이자율은 로그노말 분포를 따르며, 채권가격 및 스왑션 가격을 해석적인 방법으로 결정하기 어렵고, 해찾기 등을 통해서 산출해야 함.&#x20;

</details>
