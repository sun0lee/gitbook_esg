# AFNS

## 1. 개요&#x20;

### 금리 위험액 측정 &#x20;

K-ICS 금리위험액은 1년간 금리변동에 따른 위험을 의미함. 이 위험액을 산출하기 위해 금리충격시나리오를 생성하며, 해당 금리충격시나리오에서 순자산치의 변동을 이용하여 금리위험액을 측정함. 이때 충격시나리오는 향후 1년간 99.5% 신뢰수준에서 발생할 리스크량이 되도록 산출함.&#x20;

금리 충격 시나리오를 산출하기 위한 금리모형은 **AFNS모형**을 기반으로 하고 있음. AFNS모형은 DNS모형에 무차익거래 조건이 추가된 모형임.&#x20;

**DNS 모형** :  특정시점의 금리 기간구조를 설명하는 Nelson-siegel 모형에 시간에 따른 확률 과정을 추가하여 미래 금리 기간구조를 예측하는 모형으로, 특정 시점의 금리 기간구조를 (**수준, 기울기, 곡도**) 라는 3개의 상태변수로 구분하여 설명함. 즉, 관측되지 않은 상태변수(**수준, 기울기, 곡도**) 변화의 예측을 기반으로 관측변수 (**미래 금리 기간구조**)의 변화를 예측하는 모형.

{% hint style="info" %}
### K-ICS 금리위험액 측정&#x20;



금리위험액 = $$\sqrt{max(상승손실, 하락손실)^2 + max(평행손실, 경사손실)^2)}$$ + 평균회귀로 인한 이익/손실

* 1 year 99.5% 신뢰수준의 충격 시나리오 산출.&#x20;
* 금리 충격의 종류는 아래와 같이 5가지로 구분됨
  * **평균회귀** 시나리오 ; 순자산가치 상승과 하락 모두  반영&#x20;
  * 수준 리스크 (**금리상승**시나리오 / **금리하락**시나리오) ; 순자산 가치 하락만 반영  &#x20;
  * 기울기 리스크 (**금리경사**시나리오 / **금리평탄**시나리오) ; 순자산 가치 하락만 반영 &#x20;
* (QIS) 최근 금리변동에 따라 추가적인 금리 충격 시나리오가 추가되기도 함.&#x20;
  * YTM  + 100bp up/down 등 &#x20;
{% endhint %}

### 평가 대상 및 필요 할인율 시나리오&#x20;

한편, 금리위험액은 순자산의 가치변동을 산출하기 때문에 부채 평가 할인율 뿐만 아니라. 자산의 가치변동을 측정할 수 있도록 자산 평가 시나리오에도 금리 충격시나리오를 적용해야 함. &#x20;





## 2. DNS, AFNS 모형의 구조

### 상태공간(  state - space )모형&#x20;

미관측 잠재요인(수준,  기울기,  곡도 ) 으로   관측변수(금리)를  설명하는 모형. 아래 3가지 미관측 잠재요인에 따라 실제 관측되는 만기별 이자율 기간구조를 설명함.&#x20;

$$y_t(\tau_n) =  L_t\cdot 1 + S_t \cdot\bigg(\frac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}\bigg)+C_t\cdot\bigg(\frac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}-e^{-\lambda\tau_n}\bigg)  +\epsilon_t$$

* 각 요인에 대한 loading(계수, 부하)의 의미
  * **수준 ; level** $$L_t$$ :  상수 (1), 시간의 흐름에 따라 지수적으로 감소를 보이지 않으므로, 장기적인 금리 수준 (level)으로 해석
  * **기울기 ; slope** $$S_t$$ : $$\tau$$에 대한 감소함수 $$\bigg(\frac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}\bigg)$$, 1에서 시작하여 점차 감소하므로 단기적인 요인을 의미하며 금리곡선의 기울기 (Slope)
  * **곡도 ; curvature** $$C_t$$ :  $$\tau$$에 대한 오목함수  $$\bigg(\frac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}-e^{-\lambda\tau_n}\bigg)$$, 0에서 시작해서 증가하다가 다시 감소하여 0으로 수렴하는 형태로 중기적 요인을 의미하며 금리곡선의 곡률 (Curvature) 요인으로 해석함.&#x20;
* 각 요인$$X_t (L_t, S_t, C_t)$$ 는 시간에 따라 다른 값을 갖기 때문에 시간의 흐름에 따라 금리 곡선을 생성할 수 있음.&#x20;



### 모형 비교 (DNS vs. AFNS)&#x20;

AFNS모형은 DNS모형에 무차익거래 조건이 추가된 모형으로 차익거래가 발생하지 않도록 조정항$$\frac{A(\tau)}{\tau}$$이 포함됨. &#x20;

<table><thead><tr><th width="111">금리모형</th><th>관측방정식 measurement eq</th><th>상태방정식 state eq</th></tr></thead><tbody><tr><td>DNS (disc)</td><td><span class="math">y_t(\tau)= B(\tau)X_t + \epsilon_i(\tau)</span></td><td><span class="math">X_t - \mu_X = \phi_X(X_{t-1} - \mu_X) + \eta_{Xt}</span></td></tr><tr><td>AFNS (Cont)</td><td><span class="math">y_t(\tau)= B(\tau)X_t +\dfrac{A(\tau)}{\tau}+ \epsilon_i(\tau)</span></td><td><span class="math">dXt = \Kappa ^P (\theta^P-X_t)dt + \sum dW_t^P</span>   ( <span class="math">P</span>측도 하의  연속시간모형 )</td></tr><tr><td>AFNS (disc)</td><td><span class="math">y_t(\tau)= B(\tau)X_t +\dfrac{A(\tau)}{\tau}+ \epsilon_i(\tau)</span></td><td><span class="math">X_t = (I - e^{-K^P\Delta t})\theta^P + e^{-K^P \Delta t}X_{t-1} + \eta_{Xt}</span></td></tr></tbody></table>

{% tabs %}
{% tab title="DNS  (disc)" %}
#### 관측방정식

* $$B(\tau)= \begin{bmatrix}    1 & (\frac{1-e^{-\lambda \tau_1}}{\lambda \tau_1}) & (\frac{1-e^{-\lambda \tau_1}}{\lambda \tau_1} - e^{-\lambda \tau_1}) \\    1 & (\frac{1-e^{-\lambda \tau_2}}{\lambda \tau_2}) & (\frac{1-e^{-\lambda \tau_2}}{\lambda \tau_2} - e^{-\lambda \tau_2}) \\  & \vdots &  \\ 1 & (\frac{1-e^{-\lambda \tau_N}}{\lambda \tau_N}) & (\frac{1-e^{-\lambda \tau_N}}{\lambda \tau_N} - e^{-\lambda \tau_N}) \end{bmatrix}$$: 관측방정식의 계수 행렬&#x20;
* $$X_t= \begin{bmatrix}    L_t \\ S_t \\ C_t  \end{bmatrix}$$: 추정모수&#x20;
* $$\epsilon_i(\tau) ~ N(0_{N \times 1},H_{N \times N})$$ : 오차항&#x20;
* $$H =  \begin{bmatrix}    \phi_L & 0 &  0 \\   0 & \phi_S & 0 \\ 0&0&\phi_C \end{bmatrix}$$: 공분산 행렬

#### 상태방정식

* $$\mu_X =  \begin{bmatrix}    \mu_L \\ \mu_S \\ \mu_C \end{bmatrix}$$ : 3요인의 장기평균모수&#x20;
* $$\phi_X =  \begin{bmatrix}    \phi_L & 0 &  0 \\   0 & \phi_S & 0 \\ 0&0&\phi_C \end{bmatrix}$$ : 상태방정식 계수행렬 : 3요인의자기회귀모수&#x20;
{% endtab %}

{% tab title="AFNS  (conti.)" %}
#### 관측방정식

* $$B(\tau)= \begin{bmatrix}    1 & (\frac{1-e^{-\lambda \tau_1}}{\lambda \tau_1}) & (\frac{1-e^{-\lambda \tau_1}}{\lambda \tau_1} - e^{-\lambda \tau_1}) \\    1 & (\frac{1-e^{-\lambda \tau_2}}{\lambda \tau_2}) & (\frac{1-e^{-\lambda \tau_2}}{\lambda \tau_2} - e^{-\lambda \tau_2}) \\  & \vdots &  \\ 1 & (\frac{1-e^{-\lambda \tau_N}}{\lambda \tau_N}) & (\frac{1-e^{-\lambda \tau_N}}{\lambda \tau_N} - e^{-\lambda \tau_N}) \end{bmatrix}$$: 관측방정식의 계수 행렬&#x20;
* $$X_t= \begin{bmatrix}    L_t \\ S_t \\ C_t  \end{bmatrix}$$: 추정모수&#x20;
* $$\epsilon_i(\tau) ~ N(0_{N \times 1},H_{N \times N})$$ : 오차항&#x20;
* $$H =  \begin{bmatrix}    \phi_L & 0 &  0 \\   0 & \phi_S & 0 \\ 0&0&\phi_C \end{bmatrix}$$: 공분산 행
* &#x20;$$\dfrac{A(\tau)}{\tau}$$ : <mark style="background-color:red;">**무차익거래 조정항**</mark> : 채권가격 결정 시 차익거래가 발생하지 않아야 한다는 이론적 제약을 도입하여 모형을 전개하면 도출되는 항. ( $$\tau , \lambda , \Sigma$$ )으로 구할 수 있는 closed form.

#### 상태방정식

* $$K^P = \begin{bmatrix}    K_{11}^P & 0 &  0 \\   0 & K_{22}^P& 0 \\ 0&0&K_{33}^P \end{bmatrix}$$, 평균회귀속도 모수행렬&#x20;
* $$\theta^P=\begin{bmatrix}  \theta_1^P  \\  \theta_2^P \\ \theta_3^P \end{bmatrix}$$ 장기평균모수 벡터&#x20;
* $$\Sigma= \begin{bmatrix}    \sigma_{11} & 0 & 0 \\   \sigma_{21} & \sigma_{22} & 0 \\ \sigma_{31} & \sigma_{32} & \sigma_{33} \end{bmatrix}$$ 공분산 행렬의 촐레스키 하방 삼각 행렬, 변동성 행렬&#x20;
* $$W_t^P$$ 표준 위너프로세스
{% endtab %}
{% endtabs %}



## 3. 모수추정

* 관측방정식은 요인에 대한 선형모형으로 표현되므로 칼만필터를 이용하여 모수를 추정함.&#x20;
* 상태-공간 모형의 모수 추정에 주로 사용되는 칼만필터(Kalman filter)를 이용한 최우추정법(Maximum likelihood method)을 통해 모수를 추정&#x20;

### 0) Calibration 전제조건&#x20;

* week 단위의 시계열 데이터 사용 ((ICS) 2010년부터 주별 무위험수익률 시계열 자료 활용 -> 2008 금융위기 이후 통화정책 변화를 고려하여 2010 년 이후 데이터를 활용)
* 관측방정식의 오차항을 만기별 독립
* 동일한 분산($$\epsilon$$)으로 가정
* 총 14개의 모수 추정  ( $$\lambda, \kappa_1, \kappa_2, \kappa_3, \theta_1, \theta_2, \theta_3,  \sigma_{11}, \sigma_{21}, \sigma_{22}, \sigma_{31}, \sigma_{32}, \sigma_{33}, \epsilon$$ )

### 1) 초기모수 산출

횡단면 금리기간구조와 상태변수의 시계열 변화 (전이과정)를 독립적으로 분해하여 단계적으로 모수를 추정함.&#x20;

* 개별 요인이 $$X_t (L_t, S_t, C_t)$$ 평균회귀모형을 따른다고 가정하고 모수를 우선 추정

#### (step 1) 각 시점별 상태변수 $$X_t (L_t, S_t, C_t)$$와 $$\lambda$$ 추정&#x20;

Nelson-Siegel 모델의 $$\lambda$$ 가 결정되면 $$L_t, S_t, C_t$$ 의 값은 회귀분석을 통해 산출 할 수 있음. (t 시점 넬슨시겔 모형에 따른 상태변수를 추정하여 상태변수의 시계열 데이터 생성)

* 각 시점별 상태변수 $$X_t (L_t, S_t, C_t)$$와 $$\lambda$$ 추정.&#x20;
* 넬슨시겔 모형에서 spot rate는 상태변수 (요인)와 요인 부하량의 선형결합으로 설명되므로&#x20;
* $$\lambda_0=0.5$$값을 최소자승법 (OLS) 최적화를 통해 시점별 상태변수 산출.
* $$\begin{pmatrix} L_t \\S_t\\ C_t \end{pmatrix}= argmin_{(L_t,S_t, C_t)} \displaystyle\sum_{n=1}^N\Big[y_t(\tau_n)^{mkt} - \Big\{L_t + S_t \big(\tfrac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}\big)+C_t\big(\tfrac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}-e^{-\lambda\tau_n}\big) \Big\} \Big]^2$$

#### (step 2) 최적 $$\lambda$$ 값 산출

* 전 시점의 오차제곱합을 합산한 전체 오차제곱합을 최소화 시키는 $$\lambda$$값을 최적화를 통해 산출.&#x20;
  * $$\epsilon_t =   \displaystyle\sum_{t=1}^T\displaystyle\sum_{n=1}^N\Big[y_t(\tau_n) - \Big\{L_t + S_t \big(\tfrac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}\big)+C_t\big(\tfrac{1-e^{-\lambda\tau_n}}{\lambda\tau_n}-e^{-\lambda\tau_n}\big) \Big\} \Big]^2$$
  * $$\lambda^{opt} = argmin_{\lambda} \displaystyle\sum_t^T \epsilon_t$$

#### (step 3) 회귀분석 (시계열  모수 추정) &#x20;

t+1 시점 상태변수의 확률과정의 모수를 독립적으로 추정&#x20;

* 상태방정식을 이산화하여 변동성항은 제외하면 차기 상태변수와 당기 상태변수가 선형관계가 됨.&#x20;
* $$X_{t+1} = (I_{(3)}-e^{-\kappa})\cdot\theta + e^{-\kappa}\cdot X_t$$
  * $$L_{t+1} = \beta_{L,1} +\beta_{L,2}\cdot L_t + \epsilon^L_{t+1}$$
  * $$S_{t+1} = \beta_{S,1} +\beta_{S,2}\cdot S_t + \epsilon^S_{t+1}$$
  * $$C_{t+1} = \beta_{C,1} +\beta_{C,2}\cdot C_t + \epsilon^C_{t+1}$$
* 최소자승법 (OLS) 최적화를 통해 $$\beta_{i,1}, \beta_{i,2}$$ 추정&#x20;
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
* $$\epsilon = 0.001$$

### 2) 모수 최적화&#x20;

kalman filter 를 이용한 최우추정법 (Maximum likelihood method)으로 모수 추정.  ( 칼만필터를 이용하여 계산한 로그우도값이 최대인 모수의 집합을 최적화 방식을 통해 추정 )

<details>

<summary>칼만필터</summary>



시간의 흐름에 따라 반복작업.&#x20;

* 상태방정식을 이용한 예측,&#x20;
* 관측방정식에 따른 예측오차의 보정을 반복적으로 수행
* 하며 파라미터 추정치와 미관측요인 $$X_t$$의 추정치를 구함.&#x20;

1. 초기모수&#x20;
   * $$X_{1|0} = \theta^P$$
   * $$V_{1|0} =  (I - e^{-K^P   \Delta t})^{-1} \cdot \Sigma_\eta$$
   * $$\psi_0 \equiv (I -  e^{-K^P   \Delta t}) \cdot \theta^P$$
   * $$\psi_1 \equiv e^{-K^P   \Delta t}$$
2. 칼만 필터에 의한 반복 계산 과정&#x20;
   * 조건부 추정치&#x20;
     * $$X_{t|t-1} = \psi_0 + \psi_1 \cdot X_{t-1|t-1}$$
     * $$V_{t|t-1} = \psi_1 \cdot V_{t-1|t-1} \cdot \psi_1^T  + \Sigma_\eta$$
   * 조건부 예측오차&#x20;
     * &#x20;

</details>

### &#x20;



{% file src="../../../.gitbook/assets/금융감독연구 제6권 제2호_K-ICS 2.0 무차익 DNS 금리충격 시나리오 산출 방법론의 이해와 검토_이상헌, 기호삼_.pdf" %}
