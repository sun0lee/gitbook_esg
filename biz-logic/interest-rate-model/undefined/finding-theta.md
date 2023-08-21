---
description: 계단식상수로 정의된 모수를 이용해서 theta 구하기
---

# finding theta

<figure><img src="../../../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

### input

*   $$\alpha$$는 단기구간(20년 미만)과 장기구간(20년 이후)으로 구분된 piecewise constant.

    $$\alpha(t) = \begin{cases}    \alpha_1 &\text{if } t \lt \tau \\    \alpha_2 &\text{if } t \ge \tau  \end{cases}$$
* $$\sigma$$는 스왑션 만기구간(1,2,3,5,7,10,이후)에 따라 구분된 piecewise constant.$$\sigma(t) = \begin{cases}   \sigma_1 &\text{if } t_0 \le t \lt t_1 \\    \sigma_2 &\text{if } t_1 \le t \lt t_2 \\   \cdots \\ \sigma_{M-1} &\text{if } t_{M-2} \le t \lt t_{M-1} \\    \sigma_M &\text{if } t_{M-1} \le t   \end{cases}$$

### calculation

위의 모수를 이용해 수치적분

* $$A(t,T) = e^{- \int_t^u \alpha(v)dv}$$
* $$B(t,T) = \displaystyle\int_t^T e^{-\int_t^u\alpha(v)dv}du$$
* $$Z(t)= \displaystyle \int_0^t \sigma(u)^2 \cdot e^{-\int_u^t \alpha(v)dv} \cdot B(u,t)du$$
* $$\xi(t) = \displaystyle \int_0^t \sigma(u)^2 \cdot e^{-2\int_u^t\alpha(v)dv}du$$
*   $$\Omega(t,T) = \displaystyle\int_0^t \sigma(u)^2 \{ B(u,t)^2 - B(u,T)^2 \}du$$





$$A(t,T) = \begin{cases} e^{-\alpha_1(T-t)}  &\text{if } T \lt \tau \\    e^{-\alpha_2(T-t)} &\text{if } t \ge \tau \\ e^{-\alpha_1(\tau-t)-\alpha_2(T-\tau)} &\text{if } t \le \tau  \le T \end{cases}$$



$$B(t,T) = \begin{cases}  \dfrac {1-e^{-\alpha_1(T-t)}}{\alpha_1}   &\text{if } T \lt \tau \\ \\  \dfrac {1-e^{-\alpha_2(T-t)}}{\alpha_2} &\text{if } t \ge \tau \\ \\  e^{-\alpha_1(\tau -t)} \cdot \dfrac {1-e^{-\alpha_2(T-\tau)}}{\alpha_2}  &\text{if } t \le \tau  \le T  \end{cases}$$



$$Z(t,T) = \begin{cases}   \displaystyle \int_0^t \sigma(u)^2 \cdot e^{-\alpha_1(t-u)}\cdot  \dfrac {1-e^{-\alpha_1(T-t)}}{\alpha_1} du    &\text{if } t \lt \tau \\  \\    e^{-\alpha_2(t-\tau)} \cdot  \displaystyle \int_0^ \tau \sigma(u)^2 \cdot e^{-\alpha_1(\tau-u)}\cdot  \{\dfrac {1-e^{-\alpha_1(\tau-u)}}{\alpha_1}\}du \\ + e^{-\alpha_2(t-\tau)}  \displaystyle \int_0^ \tau \sigma(u)^2 \cdot e^{-\alpha_1(\tau-u)}\cdot  \{\dfrac {1-e^{-\alpha_2(t-\tau)}}{\alpha_2}du\} \\ + \displaystyle \int_\tau^ t \sigma(u)^2 \cdot e^{-\alpha_2(t-\tau)}\cdot \{\dfrac {1-e^{-\alpha_2(t-u)}}{\alpha_2}\}du   &\text{if } t \ge \tau   \end{cases}$$





> 참고 site

{% embed url="https://www.r-bloggers.com/2021/06/hull-white-1-factor-model-using-r-code/" %}
