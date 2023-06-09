# parameter calibration

## calibration 전제   조건&#x20;

* week 단위의 시계열 데이터 사용&#x20;
* 관측방정식의 오차항을 만기별 독립
* 동일한 분산($$\epsilon$$)으로 가정
*   &#x20;총 14개   ( $$\lambda, \kappa_1, \kappa_2, \kappa_3, \theta_1, \theta_2, \theta_3,  \sigma_{11}, \sigma_{21}, \sigma_{22}, \sigma_{31}, \sigma_{32}, \sigma_{33}, \epsilon$$ )

    의 모수 추정

## 초기모수 산출

횡단면 금리기간구조와 상태변수의 시계열 변화 (전이과정)를 독립적으로 분해하여 단계적으로 모수를 추정함.&#x20;

### 금리 모형 개요&#x20;

* $$y_t(\tau_n) =  L_t\cdot 1 + S_t \cdot\bigg(\frac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}\bigg)+C_t\cdot\bigg(\frac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}-e^{-\lambda\tau_n}\bigg)  +\epsilon_t$$
  * 각 요인$$X_t (L_t, S_t, C_t)$$ 는 시간에 따라 다른 값을 갖기 때문에 시간의 흐름에 따라 금리 곡선을 생성할 수 있음.&#x20;
  * 각 요인에 대한 loading(계수, 부하)의 의미
    * $$L_t$$ :  상수 (1), 시간의 흐름에 따라 지수적으로 감소를 보이지 않으므로, 장기적인 금리 수준 (level)으로 해석
    * $$S_t$$ : $$\tau$$에 대한 감소함수 $$\bigg(\frac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}\bigg)$$, 1에서 시작하여 점차 감소하므로 단기적인 요인을 의미하며 금리곡선의 기울기 (Slope)
    * $$C_t$$ :  $$\tau$$에 대한 오목함수  $$\bigg(\frac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}-e^{-\lambda\tau_n}\bigg)$$, 0에서 시작해서 증가하다가 다시 감소하여 0으로 수렴하는 형태로 중기적 요인을 의미하며 금리곡선의 곡률 (Curvature) 요인으로 해석함.&#x20;
  * 개별 요인이 $$X_t (L_t, S_t, C_t)$$ 평균회귀모형을 따른다고 가정하고 모수를 우선 추정

### (step 1) 각 시점별 상태변수 $$X_t (L_t, S_t, C_t)$$와 $$\lambda$$ 추정&#x20;

Nelson-Siegel 모델의 $$\lambda$$ 가 결정되면 $$L_t, S_t, C_t$$ 의 값은 회귀분석을 통해 산출 할 수 있음. (t 시점 넬슨시겔 모형에 따른 상태변수를 추정하여 상태변수의 시계열 데이터 생성)

*
* 각 시점별 상태변수 $$X_t (L_t, S_t, C_t)$$와 $$\lambda$$ 추정.&#x20;
* 넬슨시겔 모형에서 spot rate는 상태변수 (요인)와 요인 부하량의 선형결합으로 설명되므로&#x20;
* $$\lambda_0=0.5$$값을 최소자승법 (OLS) 최적화를 통해 시점별 상태변수 산출.
* $$\begin{pmatrix} L_t \\S_t\\ C_t \end{pmatrix}= argmin_{(L_t,S_t, C_t)} \displaystyle\sum_{n=1}^N\Big[y_t(\tau_n)^{mkt} - \Big\{L_t + S_t \big(\tfrac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}\big)+C_t\big(\tfrac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}-e^{-\lambda\tau_n}\big) \Big\} \Big]^2$$

### (step 2) 최적 $$\lambda$$ 값 산출

*   전 시점의 오차제곱합을 합산한 전체 오차제곱합을 최소화 시키는 $$\lambda$$값을 최적화를 통해 산출.&#x20;

    * $$\epsilon_t =   \displaystyle\sum_{t=1}^T\displaystyle\sum_{n=1}^N\Big[y_t(\tau_n) - \Big\{L_t + S_t \big(\tfrac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}\big)+C_t\big(\tfrac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}-e^{-\lambda\tau_n}\big) \Big\} \Big]^2$$
    * $$\lambda^{opt} = argmin_{\lambda} \displaystyle\sum_t^T \epsilon_t$$
    *

        <figure><img src="../../../.gitbook/assets/image (89) (1).png" alt=""><figcaption></figcaption></figure>



### (step 3) 회귀분석 (시계열  모수 추정) &#x20;

t+1 시점 상태변수의 확률과정의 모수를 독립적으로 추정&#x20;

* 상태방정식을 이산화하여 변동성항은 제외하면 차기 상태변수와 당기 상태변수가 선형관계가 됨.&#x20;
* $$X_{t+1} = (I_{(3)}-e^{-\kappa})\cdot\theta + e^{-\kappa}\cdot X_t$$
  * $$L_{t+1} = \beta_{L,1} +\beta_{L,2}\cdot L_t + \epsilon^L_{t+1}$$
  * $$S_{t+1} = \beta_{S,1} +\beta_{S,2}\cdot S_t + \epsilon^S_{t+1}$$
  * $$C_{t+1} = \beta_{C,1} +\beta_{C,2}\cdot C_t + \epsilon^C_{t+1}$$
* 최소자승법 (OLS) 최적화를 통해 $$\beta_{i,1}, \beta_{i,2}$$ 추정&#x20;
*

    <figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
* 추정된  $$\beta_{i,1} \textstyle { : intercept}, \beta_{i,2} \textstyle { : slope}$$ 를 이용해 $$\kappa, \theta$$의 초기값 산출&#x20;
  * $$\kappa_i = -\frac{ln\beta_{i,2}}{\Delta t}$$, $$\theta_i = \frac{\beta_{i,1}}{1-e^{-\kappa_i \Delta t}} = \frac{\beta_{i,1}}{1-\beta_{i,2} }$$



* 추정되지 않은 모수의 초기값
  * $$\Sigma = \begin{bmatrix} \sigma_{11}, 0  , 0\\ \sigma_{21}, \sigma_{22}, 0 \\ \sigma_{31},\sigma_{32},\sigma_{33} \end{bmatrix}$$
    * $$e=\begin{bmatrix} e_{L_1},e_{L_2},e_{L_3},...,e_{L_{N-1}}\\ e_{S_1},e_{S_2}, e_{S_3},...,e_{S_{N-1}}\\ e_{C_1}, e_{C_2}, e_{c_3},...,e_{C_{N-1}} \end{bmatrix}$$
      * $$e_{L_n} = Y_{L_n} - (\hat\beta_{L1} + \hat\beta_{L2}\cdot X_{L_n})$$
      * $$e_{S_n} = Y_{S_n} - (\hat\beta_{S1} + \hat\beta_{S2}\cdot X_{S_n})$$
      * $$e_{C_n} = Y_{c_n} - (\hat\beta_{C1} + \hat\beta_{C2}\cdot X_{C_n})$$
  * $$\hat \Omega  = \frac{1}{N-3}\cdot e \cdot e^T$$&#x20;
  * $$\hat \Sigma = \frac{chol(\hat\Omega)}{\sqrt{\Delta_n}}$$ 으로 설정&#x20;
  * > DNS 모형을 통한 금리 충격 시나리오 산출 및 분석 P.11 참고&#x20;

<figure><img src="../../../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

* $$\epsilon = 0.001$$

## 모수 최적화&#x20;

kalman filter 를 이용한 최우추정법 (Maximum likelihood method)으로 모수 추정.  ( 칼만필터를 이용하여 계산한 로그우도값이 최대인 모수의 집합을 최적화 방식을 통해 추정 )







> 참고문서 : 리스크보고서 2018-2 금리리스크 산출시스템 통합 및 개선&#x20;
