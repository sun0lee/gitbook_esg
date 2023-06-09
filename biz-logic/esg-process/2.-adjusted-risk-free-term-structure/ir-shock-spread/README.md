---
description: 금리충격스프레드 ; K-ICS 금리 위험액을 산출용 결정론적 금리시나리오 생성을 위한 충격 스프레드.
---

# IR Shock Spread

자체적으로 충격 시나리오를 산출하는 경우와 금감원에서 제공한 충격스프레드를 적용하는 경우 두 가지 방법을 지원함. ( 금감원 제공 스프레드를 우선 적용함 )

<table data-column-title-hidden data-view="cards"><thead><tr><th>적용</th><th>Table</th><th align="right">Job</th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><em><strong>금리이력정보 생성  (hist)</strong></em></td><td>AFNS 모수산출용 </td><td align="right">210 </td><td><a href="ir-historical.md">ir-historical.md</a></td></tr><tr><td><em><strong>자체 산출 금리충격스프레드</strong></em></td><td>AFNS </td><td align="right">220 </td><td><a href="calculate-shock-spread.md">calculate-shock-spread.md</a></td></tr><tr><td><em><strong>금감원 제공 금리충격스프레드</strong></em></td><td></td><td align="right">230</td><td><a href="apply-shock-spread.md">apply-shock-spread.md</a></td></tr></tbody></table>







{% hint style="info" %}
### 금리충격 스프레드 산출 목적 및 충격 시나리오

K-ICS 금리위험액은 1년간 금리변동에 따른 위험을 의미함. 이 위험액을 산출하기 위해 금리충격시나리오를 생성하며,  이는 향후 1년간 99.5% 신뢰수준에서 발생할 리스크량이 되도록 산출함.
{% endhint %}

<details>

<summary>금리 충격시나리오는  (상승, 하락, 평탄, 경사, 회귀)의 5개의 결정론적 금리충격시나리오로 구성.</summary>

1. **금리상승시나리오**

금리기간구조가 전반적으로 상승하는 위험 (LTFR 15bp 상승 가정)

2. **금리하락시나리오**

금리기간구조가 전반적으로 하락하는 위험 (LTFR 15bp 하락 가정)

3. **금리평탄시나리오**

금리기간구조가 단기금리는 상승하고 장기금리는 하락하는 위험 (금융위기 발생 시 단기금리는 급등하고 장기금리는 하락하여 장단기 금리 역전현상이 발생하는 경우도 많음 )

4. **금리경사시나리오**

금리기간구조가 단기금리는 하락하고 장기금리는 상승하는 위험

5. **평균회귀시나리오**

평균적인 금리수준으로 회귀하는 금리변동의 특성을 금액으로 환산한 값(이하 ‘평균회귀금액’)을 반영 (최근 금리가 많이 하락한 국가는 향후 금리가 조정되는 과정에서 금리가 상승할 가능성이 높으므로 평균회귀시나리오를 추가하여 금리하락 충격시나리오가 과도하게 산출되는 현상을 보완할 수 있음)

</details>

> ### 금리충격 스프레드 산출 절차&#x20;

<details>

<summary>1. 금리모델 확정 (무차익모형 vs. 균형모형)</summary>

금리커브를 추정할 수 있는 금리모델(이자율 기간구조 추정)을 확정해야 함 (무차익모형 vs. 균형모형)

K-ICS의 경우, 금리모델을 무차익모델 중 하나인 AFNS로 선정함.&#x20;

* 기준일자의 금리커브에 fitting 시키기 위해 No Arbitrage model을 적용함 (시장정보와 일관된 추정)
* 기준일자의 금리커브를 제약조건으로 하기 때문에 평가시점에 따라 모수가 변경됨 (time-varing parameter)&#x20;
* 금리커브의 변동을 설명할 수 있는 세 가지 요인 (level 수준, slope 기울기,coverture 곡률) &#x20;

</details>

<details>

<summary>2.AFNS 모형에 대한 모수추정</summary>

DNS 모형 적용시 모수추정&#x20;

* L, S, C는 실제로 관측되지 않는 변수로 모형 추정 시에는 state space model로 나타냄.
* 회귀분석을 통해 L, S를 산출, 임의의 초기모수값  𝜆 세팅
* 로그 우도함수가 최대가 되는 모수집합 추정. (Kalman filter 알고리즘을 통해 추정)
  * 최대우도법(Maximum Likelihood Estimation, 이하 MLE)은 모수적인 데이터 밀도 추정 방법으로써 파라미터 θ=( θ 1 , ⋯ , θ m ) 으로 구성된 어떤 확률밀도함수 P ( x | θ ) 에서 관측된 표본 데이터 집합을 x=( x 1 , x 2 , ⋯ , x n ) 이라 할 때, 이 표본들에서 파라미터 θ=( θ 1 , ⋯ , θ m ) 를 추정하는 방법 ?

DNS -> AFNS 모형 확장 &#x20;

* 시간에 따른 변동성 조정항 반영 (초기모수는 DNS 모형과 동일하게 추정, Kalman filter 알고리즘을 이용한 최우추정시에만 조정항 반영  )

</details>

<details>

<summary>3.충격 수준 산출 </summary>

AFNS 상태변수 L,S,C는 다변량 정규분포를 따른다고 가정.&#x20;

* K-ICS는 금리 커브변동의 설명력이 높은 L, S 두개의 요인으로만 변동성 충격을 산출함.
* 평균회귀 : 조건부 기대값&#x20;
* 상승하락 :  수준 충격     => ±𝘕¯¹(99.5%)×(cos(𝜃)𝘔𝘦₁ + sin(𝜃)𝘔𝘦₂)&#x20;
* 경사평탄 :  기울기 충격  => ±𝘕¯¹(99.5%)×(cos(𝜃)𝘔𝘦₂ - sin(𝜃)𝘔𝘦₁)&#x20;

잔존만기별 가중치 반영&#x20;

</details>

> ### Reference&#x20;

<details>

<summary>KICS 해설서 : 무위험 금리기간구조에 충격을 주는 이유</summary>

보험회사가 자산 또는 부채를 할인할 때는 무위험 금리기간구조에 위험스프레드·잔여스프레드 (자산) 또는 변동성조정(부채)을 가산한 조정 무위험 금리기간구조를 사용하지만, <mark style="color:blue;">금리위험액 산출 시에는 무위험 금리기간구조에만 충격 시나리오를 부여</mark>

* 위험스프레드 중 신용위험스프레드는 거래상대방의 부도 위험 및 등급하락 위험에 따라 산출되므로 금리리스크가 아닌 신용리스크 측정대상에 해당하며,
* 잔여스프레드(위험스프레드–신용위험스프레드) 및 변동성조정 역시 자산 포트폴리오에 내재되어 있는 신용 스프레드가 기초가 되므로 금리리스크 측정대상에서 제외

</details>

<details>

<summary> KICS 해설서 :  산출방법 (금리모형)</summary>

**AFDNS(Arbitrage Free Dynamic Nelson Siegel)**&#x20;

무위험 금리기간구조를 추정한 후, 99.5% 신뢰수준에 해당하는 충격 시나리오(평균회귀, 금리상승, 금리하락, 금리 평탄,금리경사) 도출

AFDNS 모형은 금리기간구조의 주요 변동요인인 <mark style="color:blue;">수준(level)·기울기(slope)·곡률 (curvature)</mark>을 독립변수로 이용하여 함수로 추정하고, 함수에 사용한 독립변수를 직접 이용하여 99.5% 신뢰수준 下 충격 시나리오 도출하는 방식

* &#x20;단, K-ICS는 금리기간구조 변화의 상당 부분(약 95%)을 수준(level)과 기울기(slope) 변화로 설명할 수 있으므로 금리리스크 충격 시나리오 산출 시 곡률 요인을 제외하고 수준 변화와 기울기 변화만 적용
  * 수준 변화 : 금리상승·금리하락 시나리오
  * 기울기 변화 : 금리평탄·금리경사 시나리오

</details>
