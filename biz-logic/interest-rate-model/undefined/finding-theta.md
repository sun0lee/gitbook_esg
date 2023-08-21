---
description: 계단식상수로 정의된 모수를 이용해서 theta 구하기
---

# finding theta

<figure><img src="../../../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

*   $$\alpha$$는 단기구간(20년 미만)과 장기구간(20년 이후)으로 구분된 piecewise constant.

    $$\alpha(t) = \begin{cases}    a_1 &\text{if } t \lt \tau \\    a_2 &\text{if } t \ge \tau  \end{cases}$$
* $$\sigma$$는 스왑션 만기구간(1,2,3,5,7,10,이후)에 따라 구분된 piecewise constant.$$\sigma(t) = \begin{cases}   \sigma_1 &\text{if } t_0 \le t \lt t_1 \\    \sigma_2 &\text{if } t_1 \le t \lt t_2 \\   \cdots \\ \sigma_{M-1} &\text{if } t_{M-2} \le t \lt t_{M-1} \\    \sigma_M &\text{if } t_{M-1} \le t   \end{cases}$$
* 위의 모수를 이용해 수치적분
  * $$A(t,T) = e^{- \int_t^u \alpha(v)dv}$$
  * $$B(t,T) = \displaystyle\int_t^T e^{-\int_t^u\alpha(v)dv}du$$
  * $$Z(t)= \displaystyle \int_0^t \sigma(u)^2 \cdot e^{-\int_u^t \alpha(v)dv} \cdot B(u,t)du$$
  * $$\xi(t) = \displaystyle \int_0^t \sigma(u)^2 \cdot e^{-2\int_u^t\alpha(v)dv}du$$
  *   $$\Omega(t,T) = \displaystyle\int_0^t \sigma(u)^2 \{ B(u,t)^2 - B(u,T)^2 \}du$$



