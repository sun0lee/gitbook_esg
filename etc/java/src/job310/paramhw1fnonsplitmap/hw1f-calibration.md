# Hw1f Calibration

```java
Main Function for Calibration Parameter
public List<Hw1fCalibParas> getHw1fCalibrationResultList(String mode) {

// 1st Step 1-1: Preparation of Swap Cashflow from Smith-Wilson Result from baseCurve 	
  applySmithWilsonInterpoloation(this.prjInterval, this.lastLiquidPoint);		
  
// 1st Step 1-2: Calculate Exercise Rate of Swaption and PV of Swap Cashflow
  calSwpnSwapRate();
  
// 1st Step 1-3: Calculate At The Money Price of of Swaption	
  calSwpnPriceAtm();
  
// 2nd Step of Main Process: Find Optimal HW Calibration Parameters	
  optParasHw();	
```

## 1st Step : 스왑션 가격 산출&#x20;

* [https://ojs.tripaledu.com/index.php/jefa/article/view/38/44](https://ojs.tripaledu.com/index.php/jefa/article/view/38/44)

### 스왑션 정의 &#x20;

스왑션은 특정한 날짜에 특정한 금리로 이자율 스왑 계약을 할 수 있는 옵션 (Payer와 Receiver)

이자율 스왑의 가치는 고정 이자율과 변동 이자율을 교환하는 거래의 특성 상,  고정 이자율로 인한 현금흐름과 변동 이자율로 인한 현금흐름의 차이를 고려하여 현재가치를 계산함. 잔여 이자율 지급 기간과 이자율에 따라 적립되는 이자금액의 현재 가치로 평가됨.&#x20;

* Payer 스왑션은 이자율 스왑을 할 수 있는 Call option
* Receiver 스왑션은 이자율 스왑을 할 수 있는 Put option으로 변환



일반화된 Black-Scholes 모형을 이용하여 스왑션의 가격을 계산할 수 있음. (Black-76 모형)

1. 스왑의 무위험 이자율 곡선에 따른 스왑의 가치를 계산하고,&#x20;
2. 이를 이용하여 스왑션의 가격을 계산함.&#x20;

<details>

<summary>Black-76 모형</summary>

블랙-숄즈 모형을 이용하여 주가 옵션의 가격을 산출하는 방법을 이자율 옵션에 적용한 것. 이 모형은 1976년 피셔 블랙(Fischer Black)이 개발하였으며, 이자율 옵션 가격 산출을 위한 표준적인 모형으로 사용됨.

Black-76 모형은 이자율이 고정이 아닌 변동할 때 사용함. 기초 자산이 고정 이자율 채권이며, 이자율이 변동할 때 이자율 옵션의 가격을 산출하는 모형임. 블랙-숄즈 모형과 비슷하게 Black-76 모형은 이자율 옵션의 가격을 블랙-숄즈 방정식을 이용하여 계산함.

1. 이자율은 로그 정규분포를 따른다고 가정
2. 이자율 변동성을 상수로 가정
3. 이자율의 수익률을 무위험 단기 이자율로 근사

</details>



## 2nd Step : HW Calibration Parameters (최적 모수 산출)

### Hull-White 모형 이해&#x20;

Hull-White 모형은 단기 이자율을 모델링하기 위한 이항 트리 모형 혹은 금리 모형으로, 이자율 변동성이 시간에 따라 변하는 것을 반영함.&#x20;

### 모수

* short rate volatility : 금리 변동성, 변동성이 클수록 금리가 불안정하게 움직임.&#x20;
* mean reversion speed : 평균회귀속도, 금리가 평균에 가까워지는 속도를 의미함.&#x20;
* long-term mean level : 장기평균금리, 장기적인 금리 수준을 결정함.&#x20;
* 현재 이자율 -> 시뮬레이션 시작점.&#x20;

### 모수 산출 단계&#x20;

1. swaption 가격을 이용해서 이자율 노드들의 적정 가중치를 계산
2. 산출된 가중치를 이용해서 이자율 모형을 추정
3. 추정된 모형을 이용해서 모수를 계산

### 모수 추정방법&#x20;

* 최소제곱법(least squares) : swaption 가격과 이론적으로 계산된 swaption 가격 간의 차이를 최소화하는 모수를 선택
* 최대우도법(maximum likelihood) : 주어진 swaption 가격들이 나올 확률이 가장 높은 모수를 선택

\-> 추정된 모수를 대입해서 Hull-White 모형을 이용해서 금리시나리오 예측.&#x20;
