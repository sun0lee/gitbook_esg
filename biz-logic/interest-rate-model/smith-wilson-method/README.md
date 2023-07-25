# Smith-Wilson Method

## 1.  개요

<details>

<summary>이자율 곡선의 보간 및 보외 </summary>

시장에서 관찰되는 자산의 만기 정보를 이용하여 듀레이션이 긴 보험부채를 평가하기 위해 관찰되지 않는 기간의 금리를 보간 및 보외하는 방식이 필요함.&#x20;

금리곡선 보간 보외 방식은 넬슨 시겔모형, 직선 보간 보외법, smith wilson 방법 등 여러가지 방법이 있으나, ICS 및  SolvencyII 등의 해외 주요제도에서는 smith-wilson 방식을 채택함.&#x20;

* 금리기간구조의 보간 보외가 동시에 가능한 방법
* 단기 시장금리의 변동에 따른 장기 금리의 급격한 변동을 방지함.&#x20;

</details>

Smith-Wilson 방법론은 장기선도이자율(LTFR) 이 주어진 상태에서 시장이자율을 보간 및 보외하여 무위험할인율을 산출하는 방법. 금융당국의 향후 금리에 대한 전망을 LTFR에 설정하여 반영함.&#x20;

K-ICS에서는 무위험이자율 대용치로 사용할 시장데이터를 국고채 수익률로 정함. (국내의 경우 국고채가 이자율스왑보다 거래량이 풍부하며, 금융시장의 구조적 수급 불균형으로 인해 스왑금리가 국고채 수익률보다 낮은 이상현상이 발생)

* 국고채수익률 : 정부가 공공목적에 필요한 자금확보를 목적으로 발행하는 채권으로 국가가 보증하기 때문에 무위험으로 간주함. (IAA.2013 에 따르면 경우에 따라 국고채수익률을 무위험이자율의 대용치로 사용하기에 정치적 환경, 세금 등 시장상황의 반영이 어려울 수도 있음.)
* 스왑금리 : 이자율스왑 거래 시 변동금리와 교환되는 고정금리로, 은행 간 차입금리에 해당하여 무위험에 가깝지만, 은행 간 거래에 있으서의 신용리스크가 발생. 단, 국내의 경우 중앙청산소 거래가 의무화되어 무위험에 가까움.&#x20;

## 2. 모수 설정&#x20;

{% tabs %}
{% tab title="LTFR" %}
**LTFR(LongTerm Forward Rate**) : 장기선도금리 (4.8%)

#### K-ICS 산출기준&#x20;

장기선도금리는 다음의 기준에 따라 산출한다.

* ᄀ. 장기선도기준금리는 실질이자율의 장기평균과 기대인플레이션율의 합으로 산출한다.
  * a. 실질이자율의 장기평균은 국내 지표금리에 연간 소비자물가상승률을 차감조정하여 산출 한다.
  * b. 기대인플레이션율은 장기 기대인플레이션율을 적용한다.
  * c. “a.”와 “b.”의 <mark style="background-color:green;">**세부 산출기준은 감독원장이 정한다.**</mark>
* ᄂ. 장기선도기준금리가 직전년도 장기선도금리 대비 15bps 이상 변화한 경우 당해연도 장기선도금리를 직전년도 장기선도금리 대비 15bps 상향조정(직전년도 대비 상승) 또는 하향조정(직전년도 대비 하락)한다.
* ᄃ. 장기선도기준금리가 직전년도 장기선도금리 대비 변화폭이 15bps 미만인 경우 직전년도 장기선도금리를 당해 연도에도 동일하게 적용한다.

#### 산출에 활용되는 데이터 예시&#x20;

* 실질이자율 : (CD금리–실제물가상승률)/(1+물가상승)
* 목표물가상승률 : 한국은행은 물가안정목표를 의미하며 2%로 변경 (2016\~)
{% endtab %}

{% tab title="LOT" %}
**LOT(Last Observed Term)** :  최종관찰만기&#x20;

* 부채평가 할인율 LOT : (20Y)
  * &#x20;최종관찰만기는 국고채 발행잔액, 국고채 지표물 호가 스프레드 등을 감안하여 <mark style="background-color:green;">**감독원장이 정한다.**</mark>
* 자산평가 할인율 : LOT : (50Y)
  * 시장에서 관찰되는 최장만기까지의 국고채 수익률을 Smith-Wilson 보간법으로 추정한 수익률 곡선을 사용하여 산출하며, 금융감독원장(이하 ‘감독원장’)이 제시한다.
{% endtab %}

{% tab title="CP" %}
**CP(Convergence Point)** : <mark style="background-color:green;">**수렴시점 (60Y)**</mark>

\= Max( LOT + 30년, 60년 )&#x20;
{% endtab %}

{% tab title="alpha" %}
$$\alpha$$: 수렴속도, 장기선도금리로 수렴하는 속도를 의미하며 값이 클수록 장기선도금리에 빠르게 수렴.&#x20;

* 부채 : 수렴시점 선도금리가 장기선도금리와 1bp 차이나도록 해찾기를 통해 산출함
* 자산 : 0.1&#x20;
{% endtab %}
{% endtabs %}

<div data-full-width="false">

<figure><img src="../../../.gitbook/assets/image (74).png" alt="" width="563"><figcaption><p>감독원장 제시 (2022)</p></figcaption></figure>

</div>

<div data-full-width="false">

<figure><img src="../../../.gitbook/assets/image (38).png" alt="" width="563"><figcaption><p>감독원장 제시 (2023)</p></figcaption></figure>

</div>

<figure><img src="../../../.gitbook/assets/image.png" alt="" width="563"><figcaption></figcaption></figure>

## 3. 활용 (K-ICS) &#x20;

아래의 작업에서 smith-wilson 방법론을 이용함.&#x20;

#### 1. 입력변수의 형태에 따른 구분 (YTM vs. Spot Rate)

1. 시장의 ytm을  이용하여 spot rate로 변환할 때&#x20;
   * ytm을 입력변수로 하여 보간하거나 spot rate로 변환하는 경우, K-ICS기준서의 요건에 따라 이자지급횟수를 2번으로 가정하고 있으므로 with zero coupon bond 케이스가 아닌 multiple CF로 구성된 asset 기반의 smith-wilson 모형을 적용해야 함. => `SmithWilsonKicsBts`
2. base tenor기준의 spot rate (continuous) 를 전체 만기에 대해 보간 보외 처리할 때 (결정론 /확률론)
   * 반면, spot rate (continuous)을 입력변수로 하여, 보간 보외 처리하는 경우에는 이미 spot rate형태로 변환되었기 때문에 이자지급주기는 의미 없음. zero coupon bond 기반 smith-wilson 모형을 적용함. =>  `SmithWilsonKics`

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><em><strong>wirh ZCB</strong></em></td><td><code>SmithWilsonKics</code></td><td>보간,보외 </td><td><a href="with-zero-coupon-bonds/">with-zero-coupon-bonds</a></td></tr><tr><td><em><strong>with Assets Multiple CFs</strong></em></td><td><code>SmithWilsonKicsBts</code></td><td>보간,보외 / 변환ytm2spot</td><td><a href="with-assets-generating-multiple-cf/">with-assets-generating-multiple-cf</a></td></tr></tbody></table>

#### 2. 평가대상에 따른 구분 (자산 평가 vs. 부채 평가)

평가 대상에 따라 시장데이터의 관측기간(LOT), LTFR, 수렴속도 alpha  등 파라메타가 상이하므로 아래와 같이 구분함. &#x20;

1. 자산 평가&#x20;
   *
2. 부채 평가 &#x20;
   *



참고자료&#x20;

{% file src="../../../.gitbook/assets/Extrapolation Methods of the Term Structure of Interest Rates under Solvency II.pdf" %}

