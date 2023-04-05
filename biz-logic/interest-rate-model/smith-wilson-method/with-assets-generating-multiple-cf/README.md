# with Assets generating multiple CF

<img src="../../../../.gitbook/assets/file.excalidraw (3).svg" alt="" class="gitbook-drawing">

만기별 zero coupon bond 대신 여러 개의 현금흐름으로 구성된 상품의 시장가격을 알 때, 이를 활용하여 smith-wilson 모형을 일반화함.



#### Generalized Smith-Wilson method

* _Present market value = Present value applying the UFR_ $\pm$_Correction_.

* _(참고)무이표채일 때 Correction =_ $\zeta_1\cdot(w_1) + \zeta_2\cdot(w_2)+ \dots +\zeta_N\cdot(w_N)$

* _Correction =_ $\zeta_1\cdot(\displaystyle\sum_{j=1}^J c_{1,j}\cdot w_1) + \zeta_2\cdot(\displaystyle\sum_{j=1}^J c_{2,j}\cdot w_2)+ \dots +\zeta_N\cdot(\displaystyle\sum_{j=1}^J c_{N,j}\cdot w_N)$
  
  

* 제타에 곱해지는 만기 별 가중치를 산출할 때, 기준이 되는 상품이 단일 현금흐름으로 구성된 것이 아니라 각 시점 별 현금흐름으로 구성되어 있기 때문에 이 현금흐름(이표)의 효과를 반영해야 함. 

* 시장에서 관찰가능한 $N$개의 상품 중 $i$번째 상품 $asset_i$의 $j$번째 $c_{i,j}$의 효과를 포함하기 위해 현금흐름의 금액 비중을 만기별 가중치에 반영함. 

* $Kernel_i(t) = \displaystyle\sum_{j=1}^M c_{i,j}\cdot W(t,t_j)$
  
  * $c_{i,j} : \{ c_{i,1}, c_{i,2}, ...,c_{i,M} \}$, $\footnotesize {i=(1,2,...,N)   ,  j = (1,2,...,M)}$
  * $\footnotesize W(t, u_j) = e^{-UFR \cdot(t+u_j)} \cdot \{\alpha\cdot min(t,u_j)-0.5\cdot e^{-\alpha \cdot max(t,u_j)} \cdot (e^{\alpha \cdot min(t,u_j)} - e^{-\alpha \cdot min(t,u_j)}) \}$

_산출한 조정항 (Correction)_ 을 이용하여 discount factor 정리하면

$v(0,t) =  e^{-UFR \small \cdot t} + \displaystyle\sum_{i=1}^N\zeta_i \cdot \displaystyle\sum_{j=1}^Mc_{i,j}\cdot W(t,t_j)$

시장에서 관찰되는 $i$ 번째 상품의 가격 $m_i$ 을 위에서 정의한 현가함수로 평가하면 $m_i =  \displaystyle\sum_{j=1}^Mc_{i,j} \cdot v(0,t_j)$이다.

이를 이용하여 시장에서 관측가능한 $N$개의 상품의 가격을 현가함수로 표현하면

$\footnotesize m_1 =  \displaystyle\sum_{j=1}^Mc_{1,j} \cdot \bigg(e^{-UFR \small \cdot t_j} + \displaystyle\sum_{l=1}^N\zeta_i \cdot \displaystyle\sum_{k=1}^Mc_{l,k}\cdot W(t_j,t_k)\bigg)$

$\footnotesize m_2 =  \displaystyle\sum_{j=1}^Mc_{2,j} \cdot \bigg(e^{-UFR \small \cdot t_j} + \displaystyle\sum_{l=1}^N\zeta_i \cdot \displaystyle\sum_{k=1}^Mc_{l,k}\cdot W(t_j,t_k)\bigg)$

$\vdots$

$\footnotesize m_N =  \displaystyle\sum_{j=1}^Mc_{N,j} \cdot \bigg(e^{-UFR \small \cdot t_j} + \displaystyle\sum_{l=1}^N\zeta_i \cdot \displaystyle\sum_{k=1}^Mc_{l,k}\cdot W(t_j,t_k)\bigg)$

식을 전개하면,

$\footnotesize m_1 =  \displaystyle\sum_{j=1}^Mc_{1,j} \cdot e^{-UFR \small \cdot t_j} + \displaystyle\sum_{l=1}^N \bigg(\displaystyle\sum_{k=1}^M\Big( \displaystyle\sum_{j=1}^Mc_{1,j}\cdot W(t_j,t_k)\Big)\cdot c_{l,k}\bigg)\cdot\zeta_l$

$\footnotesize m_2 =  \displaystyle\sum_{j=1}^Mc_{2,j} \cdot e^{-UFR \small \cdot t_j} + \displaystyle\sum_{l=1}^N \bigg(\displaystyle\sum_{k=1}^M\Big( \displaystyle\sum_{j=1}^Mc_{2,j}\cdot W(t_j,t_k)\Big)\cdot c_{l,k}\bigg)\cdot\zeta_l$

$\vdots$

$\footnotesize m_N =  \displaystyle\sum_{j=1}^Mc_{1,j} \cdot e^{-UFR \small \cdot t_j} + \displaystyle\sum_{l=1}^N \bigg(\displaystyle\sum_{k=1}^M\Big( \displaystyle\sum_{j=1}^Mc_{N,j}\cdot W(t_j,t_k)\Big)\cdot c_{l,k}\bigg)\cdot\zeta_l$

위의 식을 행렬식으로 표현하면

$m =  C_\mu + (CW C^T)\cdot \zeta$  이고,

시장의 가격$m$ 과 자산의 현금흐름 정보$C$ 를 알고 있으므로 $\zeta$ 를 구할 수 있음.

$\zeta = (CW C^T)^{-1}\cdot (m-C_\mu)$

현물이자율을 산출해야 하므로 무이표채권의 가격산출식 (=현가함수)에 산출한 $\zeta$를 대입하면

$v(0,t) =  e^{-UFR \small \cdot t} + \displaystyle\sum_{i=1}^N\zeta_i \cdot \displaystyle\sum_{j=1}^Mc_{i,j}\cdot W(t,t_j)$

$\hat{\zeta} = C^T \cdot \zeta$ 라고하면

$v(0,t) =  e^{\tiny -UFR  \cdot t} + \displaystyle\sum_{j=1}^N\hat{\zeta_i} \cdot W(t ,t_j) \footnotesize$ 

* K-ICS 기준서에 따라 만기별 수익률을 현물이자율로 전환시에는 수렴속도 $\alpha=0.1$을 적용함. 
