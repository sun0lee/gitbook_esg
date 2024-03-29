---
description: >-
  확률론적 시뮬레이션이란 자산의 미래가치를 특정모형을 활용하여 무작위적으로 상당수의 시나리오를 발생시킨 후, 각 시나리오 하에서 미래가치를
  프로젝션하는 방법임.
---

# Hull-White 1 Factor

## 1. 목적&#x20;

보험계약집합의 현재가치를 평가할 때, 기초항목의 성과와 연동되는 현금흐름과 그렇지 않은 현금흐름을 분리하지 않는 경우, 전체 현금흐름에 적절한 할인율을 적용해야 함. 즉, 금리변동에 따라 영향을 받는 현금흐름 효과를 측정하기 위해 확률론적 금리 시나리오 등을 통해 금리변동에 의해 발생 가능한 보증옵션의 가치를 산출함.&#x20;



## 2. 금리 모형&#x20;

조정 무위험 금리 기간구조를 기반으로 금리 시나리오 모형을 통해 산출함. (**Hull-White 1 factor 모델**, 무차익 모형이며 스왑션 변동성 데이터를 기반으로 수렴속도 모수$$\alpha$$ 와 변동성 모수$$\sigma$$를 추정하여 시나리오 생성. )

*   $$dr(t) = \alpha (t) \cdot [ \theta(t) - r(t)]dt + \sigma \cdot dW(t)$$

    &#x20;이산화 : ( $$r_{t+1} = r_t + \alpha (\theta_t -r_t) + \sigma \epsilon_t$$ )

    * $$\theta(t)$$ ; 목표금리, 관찰된 시장 금리곡선에 적합시켜 금리가 시장금리곡선을 중심으로 수렴하도록 함.
      *   $$\theta(t) = \dfrac{f_{t+1}-f_t}{\alpha_t \Delta t} + f_t + \int_0^t \sigma_u^2 \cdot e^{-2\alpha_u \cdot(t-u)}du$$

          $$= \dfrac{f_{t+1}-f_t}{\alpha_t \Delta t} + f_t + Z(t)$$

          (이때 $$Z(t)$$는 finding theta 참조)



          ( $$= \frac{\partial}{\partial t} f_t + \alpha_t f_t + \frac{1}{2} (\frac{\partial ^2}{\partial t^2} \int_t^T \sigma^2_udu +\alpha_t \frac{\partial}{\partial t} \int_t^T \sigma^2_udu)$$)


    * $$\alpha(t)$$; 회귀모수, 목표금리로 수렴하는 속도&#x20;
    * $$\sigma(t)$$; 변동성모수, 연율화된 금리의 변동성&#x20;
    * $$dW_t$$; Brownian Motion, 확률적으로 움직이는 위험 요인&#x20;



## 3. parameter

* 스왑션 데이터가 관찰되는 기간과 관찰되지 않는 기간을 구분하여 산출.&#x20;
* 모수 산출에 사용되는 스왑션 데이터는 시장에서 관찰되는 모든 스왑션 데이터(옵션만기 1년, 2년, 3년, 5년, 7년, 10년 및 스왑만기 1년, 2년, 3년, 5년, 7년, 10년에 해당하는 총 36개 데이터)를 사용한다.

{% tabs %}
{% tab title=" alpha  수렴속도 " %}
회귀모수 $$\alpha$$; 금리시나리오가 수익률 곡선에 회귀하는 속도를 결정하는 모수&#x20;

평균 회귀 계수(mean reversion coefficient) 금리가 평균 수준으로 회귀할 때 속도를 결정함. 알파가 클수록 평균수준에 빠르게 회귀. max (산출값, 0.0001)

모든 시점에서의 단기 이자율의 평균과 장기 이자율의 평균 사이의 차이&#x20;

#### $$\alpha$$ parameter 추정방법&#x20;

스왑션 데이터가 관찰되는 기간의 **수렴속도 모수**는 세부기간을 구분하지 않고 단일의 모수로 산출.

스왑션 데이터가 관찰되지 않는 기간의 수렴속도 모수 및 변동성 모수 산출 기준은 감독원장이 제시한다.

* 단기 (20년 이내)는 단일값&#x20;
* 장기(20년 초과)는 장기구간 (10년\~20년)의 <mark style="background-color:green;">**최근 10년 평균**</mark>값 사용&#x20;
{% endtab %}

{% tab title="sigma 변동성  " %}
금리의 변동성을 결정하는 모수 , 확산 계수(diffusion coefficient)

#### $$\sigma$$ parameter 추정방법&#x20;

**변동성 모수**는 0\~1년, 1\~2년, 2\~3년, 3\~5년, 5\~7년, 7\~10년 기간별로 세분화하여 모수를 산출.

스왑션 데이터가 관찰되지 않는 기간의 수렴속도 모수 및 변동성 모수 산출 기준은 감독원장이 제시한다.

* 단기(10년 이내) : 구간별로 적용
* 장기(10년 초과) : 장기구간 (7년\~10년)의 <mark style="background-color:green;">**최근 10년 평균**</mark>값 사용&#x20;
{% endtab %}

{% tab title="theta 장기평균 " %}
#### 수익률 곡선 적합함수, 목표금리 &#x20;

수익률곡선이 복원 가능하도록 조정하는 역할을 하는 함수, Yeild Curve Fitting, 관찰된 시장 금리곡선에 적합시켜 금리가 시장금리곡선을 중심으로 수렴하도록 함.&#x20;

#### parameter 추정방법&#x20;

$$\theta(t) = \dfrac{f_{t+1}-f_t}{\alpha \Delta t} + f_t + \int_0^t \sigma_i^2 e^{-2\alpha(t-u)}du$$
{% endtab %}
{% endtabs %}



<figure><img src="../../../.gitbook/assets/image (13).png" alt="" width="563"><figcaption><p>산출시점별 parameter 추정 예시 </p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (37).png" alt="" width="563"><figcaption><p>스왑션 데이터가 관찰되지 않는 기간의 모수산출기준 (감독원장 제시 2022)</p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1) (2).png" alt="" width="563"><figcaption><p>스왑션 데이터가 관찰되지 않는 기간의 모수산출기준 (감독원장 제시 2023)</p></figcaption></figure>

<details>

<summary>parameter Calibration</summary>

#### 수익률 곡선 산출방법&#x20;

* 지표금리 : IRS 스왑금리 (1,2,3,4,5,7,10,12,15,20Y)

#### 스왑션 가격 산출방법&#x20;

* 변동성으로 금리 Receiver 스왑션의 Black Vol을 이용함. (즉, 스왑션 만기시 해당 Tenor기간동안 변동금리를 지급하고 정해진 스왑금리로 고정금리를 수취하는 스왑계약을 체결할 수 있는 권리)&#x20;
  * 금리스왑션가격은 금액이 아닌 블랙모형을 이용한 ATM 내재변동성 (Black Vol)으로 고시. (ATM 내재변동성이란 스왑션 만기에 시작되고 그 만기(t)가 스왑션의 tenor($$\tau$$)가 되는 forward start 스왑의 금리를 행사가로 하는 스왑션의 내재변동성을 의미함. )&#x20;
  * Black Model 을 이용하여 스왑션을 평가하기 위해 스왑션변동성이 필요함. (시장에서 관찰되는 스왑션의 잔존만기 및 대상 스왑의 테너에 따라 ATM 스왑션의 변동성을 사용.  스왑션의 잔존만기와 스왑의 테너를 각각의 축으로 가지는 2차원 Grid Point에 해당하는 스왑션 변동성을 시장에서 호가되는 자료로 사용함. )
* Black Vol을 이용하여 스왑션 가격을 산출함.&#x20;
  * Black Model을 이용하여 스왑션 만기(6개)별 스왑테너별로 총 48개의 스왑션 가격을 산출.
  * receiverSwaption = $$N \cdot A[K\cdot N(-d_2)-Swap(t-\tau) N(-d_1)]$$
    * $$A = \sum_i P(0,t_i)$$
    * $$d_1= \dfrac{ln(\frac{Swap(t,\tau)}{K} +\frac{1}{2}\sigma^2 T)}{\sigma\sqrt{T}}$$
    * $$d_2 = d_1 -\sigma \sqrt{T}$$
    * $$t_i$$: 스왑의 이자 교환일, $$N$$은 액면가&#x20;

#### 금리모형&#x20;

* Hull-White 모형을 사용 : $$dr(t) = \alpha \cdot [ \theta(t) - r(t)]dt + \sigma \cdot dZ$$ &#x20;
  * 단기금리(Short Rate)가 위의 프로세스를 따른다고 가정.&#x20;
* Hull-White모형을 이용하여 스왑션 가격을 Closed form으 구할 수 있음 (Calibration이 용이함. )
  * Hull-White 모형 채권가격
  * Hull-White 모형 Receiver Swaption가격&#x20;

#### Calibration&#x20;

* Hull-White 모형을 Calibration 한다는 것은 스왑션 가격을 복원할 수 있는 모수 (회귀강도, 목표금리, 변동성)를 산출하는 것을 의미함.&#x20;
* 회귀강도 $$\alpha$$ 와 변동성 $$\sigma_i$$ 을 금리 스왑션의 가격으로부터 산출하기 위해 Lvenberg Marquardt 방법을 이용함.&#x20;
* (Lvenberg Marquardt : 시장의 금리 스왑션 가격과 모형을 통해 산출한 스왑션 가격의 차이의 제곱합을 최소화하는 모수를 iteration 을 통해 산출하는 방법 중 하나.)&#x20;
* $$\theta (t)$$는 선도곡선과 다른 모수( $$\alpha, \sigma_i$$)들로부터 산출

</details>



## 4. 시나리오 산출

* 단기금리(Short Rate)가 다음의 프로세스를 따른다고 가정.$$dr(t) = \alpha \cdot [ \theta(t) - r(t)]dt + \sigma \cdot dW_t$$ &#x20;
* 위의 식을 이산화하면 다음과 같음. $$r_{t+1} = r_t + \alpha (\theta(t_i, t_{i+1})-r_t)\cdot \Delta t_i + \sigma_i(t) \sqrt{\Delta t_i } \cdot \epsilon_t$$
  * 난수 $$\epsilon \thicksim N(0,1)$$를 추출하여 Calibration에 의해 산출된 모수를 적용하여 단기금리 시나리오를 산출.&#x20;



## 5. 적정성 검증&#x20;

아래 내용이 포함된 시나리오 유효성 검증 보고서를 위험리위원회에 보고해야 함.&#x20;

{% tabs %}
{% tab title="모수 적정성 " %}
* 모수 추정 방법의 유효성 : 모수는 최적해를 효율적으로 찾을 수 있는 알고리즙에 기반하여 산출&#x20;
* 시장가격 설명력 : 추정된 모수를 통해 산출한 모형가격과 시장가격의 차이가 최소화&#x20;
* 모수 추정결과의 안정성 : 시장데이터 일부를 변경하여 모수를 추정하더라도 모수가 안정적로 산출되어야 함.&#x20;
* 스왑션 데이터의 일관성 및 적합성 : 블랙 변동성과 노말 변동성 중 금리 환경 및 시나리오 추정 등에 적합한 데이터 사용
{% endtab %}

{% tab title="난수 적정성" %}
* 시나리오 간 정규성 : 난수의 분포는 매 경과기간마다 정규성 만족 &#x20;
* 경과기간별 독립성 :  난수는 경과기간별로 독립 &#x20;
* 난수 고정 사용 :&#x20;
  * 최소 10개 이상의 난수 집합을 생성한 후, 그 중 결과적정성이 가장 우월한(확률론 금리 시나리오 평균과 수익률 곡선의 편차가 가장 작은 난수 집합) 난수를 선정하고,&#x20;
  * 매 평가시점마다 동일하게 적용.&#x20;
  * 결과 적정성 검증 기준을 충족하지 못하는 경우에만 난수 변경 가능.&#x20;
{% endtab %}

{% tab title="결과 적정성 " %}
* 확률론적 금리 시나리오의 평균이 수익률 곡선과 통계적으로 일치&#x20;
* 마팅게일 테스트&#x20;
  * 미래 현금흐름의 현가가 정규분포를 따른다는 가정 하에&#x20;
  * 시나리오별 무이표채 현가의 평균과&#x20;
  * 수익률 곡선(결정론적 금리시나리오)의 무이표채 현가와 95% 신뢰수준하에서 일치&#x20;
* $$\mu$$ 가 95% 신뢰구간 $$(\bar X-1.96SE, \bar X +1.96SE)$$에 위치하면 시장 일관성이 성립한다고 판단&#x20;
  * $$\mu$$ : 수익률 곡선의 무이표채 현가
  * $$\bar X$$: 시나리오 할인 곡선의 무이표채 현가의 평균
  * $$SE$$ : 시나리오 할인 곡선의 무이표채 현가의 표준오차&#x20;
{% endtab %}
{% endtabs %}
