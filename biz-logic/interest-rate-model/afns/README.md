# AFNS

K-ICS 금리위험액은 1년간 금리변동에 따른 위험을 의미함. 이 위험액을 산출하기 위해 금리충격시나리오를 생성하며,  이는 향후 1년간 99.5% 신뢰수준에서 발생할 리스크량이 되도록 산출함.&#x20;

금리 충격 시나리오를 산출하기 위해서는 금리모델을 기반으로 충격을 반영하여 산출하는데, 이 때 기준이되는 금리모형은 AFNS모형을 기반으로 하고 있음.&#x20;



## KICS 금리모형

1. K-ICS 1.0 금리모형은 DNS 모형이었으나&#x20;
2. K-ICS 2.0 부터 무차익 조건이 추가된  DNS 모형, AFNS 모형을 기반으로 함.&#x20;



## DNS, AFNS 모형의 구조

아래 3가지 미관측 잠재요인에 따라 실제 관측되는 만기별 이자율 기간구조를 설명함.&#x20;

* 수준 ; level
* 기울기 ; slope&#x20;
* 곡도 ; curvature&#x20;

### 모수&#x20;

<details>

<summary></summary>

$$\kappa$$

```
  this.initParas[0]  = this.lambda;
  this.initParas[1]  = this.thetaL;  
  this.initParas[2]  = this.thetaS;  
  this.initParas[3]  = this.thetaC;
  this.initParas[4]  = Math.max(this.kappaL, 1e-4); 
  this.initParas[5]  = Math.max(this.kappaS, 1e-4);		
  this.initParas[6]  = Math.max(this.kappaC, 1e-4);		
  this.initParas[7]  = this.initSigma; 
  this.initParas[8]  = 0.0; 
  this.initParas[9]  = this.initSigma;
  this.initParas[10] = 0.0;            
  this.initParas[11] = 0.0; 
  this.initParas[12] = this.initSigma;
  this.initParas[13] = this.epsilon * 1000;
```

</details>

### 상태공간(  state - space )모형&#x20;

미관측 잠재요인(수준,  기울기,  곡도 ) 으로   관측변수(금리)를  설명하는 모형.&#x20;

* 관측방정식 (measurement eq.)&#x20;
* 상태방정식 (state eq.)&#x20;

<table><thead><tr><th width="111">금리모형</th><th>관측방정식</th><th>상태방정식</th></tr></thead><tbody><tr><td>DNS (disc)</td><td><span class="math">y_t(\tau)= B(\tau)X_t + \epsilon_i(\tau)</span></td><td><span class="math">X_t - \mu_X = \phi_X(X_{t-1} - \mu_X) + \eta_{Xt}</span></td></tr><tr><td>AFNS (Cont)</td><td><span class="math">y_t(\tau)= B(\tau)X_t +\dfrac{A(\tau)}{\tau}+ \epsilon_i(\tau)</span></td><td><span class="math">dXt = \Kappa ^P (\theta^P-X_t)dt + \sum dW_t^P</span>   ( <span class="math">P</span>측도 하의  연속시간모형 )</td></tr><tr><td>AFNS (disc)</td><td><span class="math">y_t(\tau)= B(\tau)X_t +\dfrac{A(\tau)}{\tau}+ \epsilon_i(\tau)</span></td><td><span class="math">X_t = (I - e^{-K^P\Delta t})\theta^P + e^{-K^P \Delta t}X_{t-1} + \eta_{Xt}</span></td></tr></tbody></table>

<details>

<summary>DNS  (disc) notation </summary>

#### 관측방정식

* $$B(\tau)= \begin{bmatrix}    1 & (\frac{1-e^{-\lambda \tau_1}}{\lambda \tau_1}) & (\frac{1-e^{-\lambda \tau_1}}{\lambda \tau_1} - e^{-\lambda \tau_1}) \\    1 & (\frac{1-e^{-\lambda \tau_2}}{\lambda \tau_2}) & (\frac{1-e^{-\lambda \tau_2}}{\lambda \tau_2} - e^{-\lambda \tau_2}) \\  & \vdots &  \\ 1 & (\frac{1-e^{-\lambda \tau_N}}{\lambda \tau_N}) & (\frac{1-e^{-\lambda \tau_N}}{\lambda \tau_N} - e^{-\lambda \tau_N}) \end{bmatrix}$$: 관측방정식의 계수 행렬&#x20;

<!---->

* $$X_t= \begin{bmatrix}    L_t \\ S_t \\ C_t  \end{bmatrix}$$: 추정모수&#x20;
* $$\epsilon_i(\tau) ~ N(0_{N \times 1},H_{N \times N})$$ : 오차항&#x20;
* $$H =  \begin{bmatrix}    \phi_L & 0 &  0 \\   0 & \phi_S & 0 \\ 0&0&\phi_C \end{bmatrix}$$: 공분산 행렬

#### 상태방정식

* $$\mu_X =  \begin{bmatrix}    \mu_L \\ \mu_S \\ \mu_C \end{bmatrix}$$ : 3요인의 장기평균모수&#x20;
* $$\phi_X =  \begin{bmatrix}    \phi_L & 0 &  0 \\   0 & \phi_S & 0 \\ 0&0&\phi_C \end{bmatrix}$$ : 상태방정식 계수행렬 : 3요인의자기회귀모수&#x20;

</details>

<details>

<summary>AFNS  (conti.) notation </summary>

#### 관측방정식

* &#x20;$$\dfrac{A(\tau)}{\tau}$$ : 무차익거래 조정항 : 채권가격 결정 시 차익거래가 발생하지 않아야 한다는 이론적 제약을 도입하여 모형을 전개하면 도출되는 항. ( $$\tau , \lambda , \Sigma$$ )으로 구할 수 있는 closed form.

#### 상태방정식

* $$K^P = \begin{bmatrix}    K_{11}^P & 0 &  0 \\   0 & K_{22}^P& 0 \\ 0&0&K_{33}^P \end{bmatrix}$$, 평균회귀속도 모수행렬&#x20;

<!---->

* $$\theta^P=\begin{bmatrix}  \theta_1^P  \\  \theta_2^P \\ \theta_3^P \end{bmatrix}$$ 장기평균모수 벡터&#x20;

<!---->

* $$\Sigma= \begin{bmatrix}    \sigma_{11} & 0 & 0 \\   \sigma_{21} & \sigma_{22} & 0 \\ \sigma_{31} & \sigma_{32} & \sigma_{33} \end{bmatrix}$$ 공분산 행렬의 촐레스키 하방 삼각 행렬, 변동성 행렬&#x20;

<!---->

* $$W_t^P$$ 표준 위너프로세스&#x20;

</details>

* AFNS 모형의 관측방정식은 요인에 대한 선형모형으로 표현되므로 칼만필터를 이용하여 모수를 추정함.&#x20;

### 칼만필터&#x20;

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



{% file src="../../../.gitbook/assets/금융감독연구 제6권 제2호_K-ICS 2.0 무차익 DNS 금리충격 시나리오 산출 방법론의 이해와 검토_이상헌, 기호삼_.pdf" %}
