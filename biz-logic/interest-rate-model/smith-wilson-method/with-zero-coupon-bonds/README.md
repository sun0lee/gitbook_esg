# with Zero Coupon Bonds

> #### Starting Point
> 
> _The present market value of an asset with a single cash flow in t is equal to the present value calculated through <mark style="color:blue;">the forward equilibrium rate</mark> to which the observed market rate converge._
> 
> * _the forward equilibrium rate = UFR ; Ultimate Forward Rate_
> 
> #### Correction is added
> 
> _in the long run the curve reflecting the forward interest rates will reach asymptotically the UFR, the Correction tends to 0 when t->_$\infin$.
> 
> * _Present market value = Present value applying the UFR_ $$\pm$$_Correction_.
> 
> * _Correction =_ $\zeta_1\cdot(w_1) + \zeta_2\cdot(w_2)+ \dots +\zeta_N\cdot(w_N)$
> 
> * $w_j$_is obtained through a Kernal function_ $Kernel_j(t)$ _that depends on the input's maturity._
> 
> * $\small W(t, u_j) = e^{-UFR \cdot(t+u_j)} \cdot \{\alpha\cdot min(t,u_j)-0.5\cdot e^{-\alpha \cdot max(t,u_j)} \cdot (e^{\alpha \cdot min(t,u_j)} - e^{-\alpha \cdot min(t,u_j)}) \}$
>   
>   * 이때 $u_i$ : 가격이 알려진 무이표채권의 만기 ; $i = \{1,2,...,N\}$
>   * 즉, $W(t, u_j)$는 t에 대한 함수로 표현되며 Wilson funtion 으로 알려짐.
> 
> 처음 아이디어  _Present market value = Present value applying the UFR_ $$\pm$_Correction_ 에서 _Correction(_보정항)을 수식으로 정리하면, 아래와 같다.
> 
> $m_t=v(0,t) =  e^{-UFR \small \cdot t} + \displaystyle\sum_{j=1}^N\zeta_j \cdot W(t , u_j)$
> 
> 시장에 이미 가격이 알려진 N개의 무이표채 가격을 이용하면 $\zeta$를 산출할 수 있으며, $p(t)$수식에 산출한 $\zeta$를 대입하면 만기 t에 대한 무이표채의 가격을 함수식으로 도출할 수 있음.
> 
> 이 관계식으로 프로젝션 하고자 하는 만기구간에 대한 할인율을 도출할 수 있음. 



### 1. $\alpha$ alpha 산출

speed of convergence (수렴속도, $$\alpha$$)는 참고 논문에서는 상수를 가정하였으나, K-ICS기준서에서는 최초 수렴시점(60y)의 선도금리와 장기목표금리 간의 차이가 1.0bp이내가 되도록 하는 최소값을 산출하여 적용하도록 함.



### 2. $\zeta$ zeta 산출

$m=v(t) =  e^{-UFR \small \cdot t} + \displaystyle\sum_{j=1}^N\zeta_j \cdot W(t , u_j)$

$\small W(t, u_j) ㅌ= e^{-UFR \cdot(t+u_j)} \cdot \{\alpha\cdot min(t,u_j)-0.5\cdot e^{-\alpha \cdot max(t,u_j)} \cdot (e^{\alpha \cdot min(t,u_j)} - e^{-\alpha \cdot min(t,u_j)}) \}$

* $N$: 최종만기($$LLP$$)까지의 현물이자율 갯수 ; 16개
* $m_i$: 무이표채권의 시장가격 ; $i = \{1,2,...,N\}$
* $u_i$ : 가격이 알려진 무이표채권의 만기 ; $i = \{1,2,...,N\}$
* $\alpha$ : 금리가 수렴하는 속도 계수 (speed of convergence)
* $W(t,u_j)$: 보정계수에 대한 만기별 가중치 ; Wilson function
* $\zeta_i$: 추정할 모수 ; $i = \{1,2,...,N\}$ (현물이자율 추정치와 시장관측치를 일치시키는 보정계수 )

시장에 알려만 각 만기 $u_i$ 에 따라 만기별 무이표채 가격의 식이 전개됨. 관측되는 만기 구간의 수가 $N$이고, 추정할 보정 계수 $\zeta$도 N개 이기 때문에 N x N 정방행렬의 형태로 구성됨.

$M = \mu +W\cdot \zeta$

* $M =  \begin{bmatrix},m_1\\m_2 \\ \vdots \\m_j\\ \vdots \\m_N \end{bmatrix}$ ,  $\mu = \begin{bmatrix}e^{-UFR\cdot t_1} \\e^{-UFR\cdot t_2} \\ \vdots\\e^{-UFR\cdot t_j}\\\vdots\\ e^{-UFR\cdot t_N} \end{bmatrix}$   , $\zeta=\begin{bmatrix} \zeta_1 \\ \zeta_2 \\ \vdots \\ \zeta_j \\\vdots\\ \zeta_N \end{bmatrix}$
* $W = \begin{bmatrix} W(t_1,t_1),W(t_1,t_2), ... , W(t_1,t_N) \\ W(t_2,t_1),W(t_2,t_2), ... , W(t_2,t_N) \\ \vdots\\ W(t_j,t_1),W(t_j,t_2), ... , W(t_j,t_N)  \\\vdots\\  W(t_N,t_1),W(t_N,t_2), ... , W(t_N,t_N) \end{bmatrix}$

벡터 $\zeta$는 관측치($M$)와 장기 선도이자율($\mu$)에 대한 차이에 벡터 $W$의 역함수를 곱하여 산출함.

$\zeta = W^{-1}\cdot(P-\mu)$



### 3. 만기별 할인율 산출

$\zeta$를 구하면, 수식  $v(t) =  e^{-UFR \small \cdot t} + \displaystyle\sum_{j=1}^N\zeta_j \cdot W(t , u_j)$는 _t_에 대한 함수로 표현되므로 모든 만기 _t_ 에 대한 할인율을 산출할 수 있음.


