# 3. Stochastic Scenario

<table data-view="cards"><thead><tr><th></th><th></th><th align="right"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><em><strong>금리모형 모수산출</strong></em> </td><td></td><td align="right">310,320,330</td><td><a href="undefined-1/310.md">310.md</a></td></tr><tr><td><em><strong>금리시나리오 생성</strong></em> </td><td></td><td align="right">340</td><td><a href="340.md">340.md</a></td></tr><tr><td><em><strong>유효성 검증</strong></em> </td><td></td><td align="right">360,370</td><td><a href="undefined-1-1/">undefined-1-1</a></td></tr></tbody></table>



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

#### 금리의 위험중립 시나리오 산출을 위한 모형&#x20;

Hull-White 모형을 사용 : $$dr(t) = \alpha \cdot [ \theta(t) - r(t)]dt + \sigma \cdot dZ$$ &#x20;
