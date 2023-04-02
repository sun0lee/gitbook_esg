# SmithWilsonKicsBts.class

## 1. spot rate 가져오기 call&#x20;

### 1.1. SmithWilsonResult -> IrCurveSpot&#x20;

SmithWilsonResult결과를 적재할 테이블 템플릿에 맞춰 변환하여 컬럼값 세팅. &#x20;

{% code title="(line17) getSpotBtsRslt()" lineNumbers="true" %}
```java
public List<IrCurveSpot> getSpotBtsRslt() {		
  
  List<IrCurveSpot> curveList = new ArrayList<IrCurveSpot>();
  
  for(SmithWilsonRslt sw : this.getSmithWilsonResultList()) {
    IrCurveSpot crv = new IrCurveSpot();
    crv.setBaseDate(sw.getBaseDate());
    crv.setMatCd.  (sw.getMatCd());
    crv.setSpotRate(sw.getSpotDisc());
    curveList.add(crv);
  }		
  return curveList;
}	
```
{% endcode %}

### 1.2. smith wilson 변환결과 call

smith wilson 변환 결과 값이 key(기준일자의 금리커브)에 따른 tenor (matCd) 단위로 한줄씩 산출되기 때문에 동일key 단위(기준일자의 금리커브 단위)로 add함. &#x20;

{% code title="(line5) getSmithWilsonResultList()" lineNumbers="true" %}
```java
public List<SmithWilsonRslt> getSmithWilsonResultList() {
  
  List<SmithWilsonRslt> resultList = new ArrayList<SmithWilsonRslt>();
  resultList.addAll(this.swProjectionList(this.alphaApplied)); // alpha= 0.1
  return resultList;
}
```
{% endcode %}

### 1.3. smith wilson 프로젝션 결과 : (alpha, tenor 입력)&#x20;

{% code title="swProjectionList(alphaApplied)" %}
```java
private List<SmithWilsonRslt> swProjectionList(double alpha) {
  return swProjectionList(alpha, this.tenor);
} 
```
{% endcode %}

* alpha = 0.1
* this SmithWilsonKicsBts (id=162)
  * \[0.25, 0.5, 0.75, 1.0, 1.5, 2.0, 2.5, 3.0, 4.0, 5.0, 7.0, 10.0, 15.0, 20.0, 30.0, 50.0]

## 2. Smith-Wilson 변환

Smith-Wilson 변환에 따라 만기에 따른 현가함수를 산출해주므로 이 결과 값은 만기코별로 한 줄 씩 산출됨.  &#x20;

{% code title="swProjectionList(alpha,tenor)" %}
```java
private List<SmithWilsonRslt> swProjectionList(double alpha, double[] prjTenor) {
  
  List<SmithWilsonRslt> swResultlList = new ArrayList<SmithWilsonRslt>();
  
  // 2.1. zetaHat	
  smithWilsonZeta(alpha);

 // 2.2. 만기별 가중치  
  RealMatrix weightPrj = MatrixUtils.createRealMatrix(smithWilsonWeight(prjTenor, this.cfCol, alpha, this.ltfrCont));

  double[] muPrj = new double[prjTenor.length];
  for(int i=0; i<muPrj.length; i++) muPrj[i] = this.zeroBondUnitPrice(this.ltfrCont, prjTenor[i]);
  
  // 2.3. zcb 가격 산출 
  RealMatrix priceCol  = weightPrj.multiply(this.zetaHat).add(MatrixUtils.createColumnRealMatrix(muPrj));
  //p(t) = e^(-ufr*t) + sigma (zetaHat * W) s.t. W = wilson-function 
  
  
  // 2.4. spotCont, fwdCont 산출 & swResult출력 
  double[] priceZcb = new double[prjTenor.length];
  double[] spotCont = new double[prjTenor.length];
  double[] fwdCont  = new double[prjTenor.length];

  for(int i=0; i<prjTenor.length; i++) {
    //zero coupon bond price
    priceZcb[i] = priceCol.getEntry(i,0);	
    spotCont[i] = -1.0 / prjTenor[i] * Math.log(priceZcb[i]); 
    spotCont[i] = Math.log(Math.exp(spotCont[i]) + this.liqPrem);
    
    // 직전 tenor와 당기 tenor를 이용해서 fwd rate산출 
    fwdCont[i]  = (i > 0) ? 
           (spotCont[i] * prjTenor[i] - spotCont[i-1] * prjTenor[i-1]) / (prjTenor[i] - prjTenor[i-1]) 
          : spotCont[i];
    
    //
    SmithWilsonRslt swResult = new SmithWilsonRslt();
    
    swResult.setBaseDate(baseDate.toString());
    swResult.setResultType("SW SPOT");
    swResult.setScenType("1");			
    swResult.setMatCd(String.format("%s%04d", TIME_UNIT_MONTH, (int) (prjTenor[i] * MONTH_IN_YEAR)));
    swResult.setDcntFactor(priceZcb[i]);
    swResult.setSpotCont(round(spotCont[i]));
    swResult.setFwdCont(round(fwdCont[i]));
    swResult.setSpotDisc(round(irContToDisc(spotCont[i])));			
    swResult.setFwdDisc(round(irContToDisc(fwdCont[i])));
    
    swResultlList.add(swResult);
  }
  return swResultlList;
}
```
{% endcode %}

### 2.1. zetaHat&#x9;

{% code title="2.1. zetaHat" %}
```java
  smithWilsonZeta(alpha);
```
{% endcode %}

<details>

<summary>2.1.1 cfColSet  @ 이자지급주기 > 0</summary>

\=> 현금흐름이 중간 중간에 발생함.  cf 발생시점 및 발생 현금흐름 기반 cf matrix 생성&#x20;

{% code title="if(this.freq > 0)" %}
```java
// cfColSet
  Set<Double> cfColSet = new TreeSet<Double>();	
  
  // 0~15 : 16개 base tenor 단위로 
  for(int i=0; i<this.tenor.length; i++) { 
    int jMax = (int) Math.ceil(this.tenor[i] * this.freq);
    
    for(int j=0; j<jMax; j++) {
      cfColSet.add(this.tenor[i] - 1.0 * j / this.freq);
    }
  }
  this.cfCol = cfColSet.stream().mapToDouble(Double::doubleValue).toArray(); 
// 102개 이자지급주기단위(freq:2)로 cf tenor 생성 
// [0.25, 0.5, 0.75, 1.0, 1.5, 2.0, 2.5, 3.0, 3.5, 4.0, 4.5, 5.0, 5.5, 6.0, 6.5, 7.0, 7.5, 8.0, 8.5, 9.0, 9.5, 10.0, 10.5, 11.0, 11.5, 12.0, 12.5, 13.0, 13.5, 14.0, 14.5, 15.0, 15.5, 16.0, 16.5, 17.0, 17.5, 18.0, 18.5, 19.0, 19.5, 20.0, 20.5, 21.0, 21.5, 22.0, 22.5, 23.0, 23.5, 24.0, 24.5, 25.0, 25.5, 26.0, 26.5, 27.0, 27.5, 28.0, 28.5, 29.0, 29.5, 30.0, 30.5, 31.0, 31.5, 32.0, 32.5, 33.0, 33.5, 34.0, 34.5, 35.0, 35.5, 36.0, 36.5, 37.0, 37.5, 38.0, 38.5, 39.0, 39.5, 40.0, 40.5, 41.0, 41.5, 42.0, 42.5, 43.0, 43.5, 44.0, 44.5, 45.0, 45.5, 46.0, 46.5, 47.0, 47.5, 48.0, 48.5, 49.0, 49.5, 50.0]
// 발생가능한 현금흐름을 관찰해야 하므로 기본 tenor를 기초로 하되 이자가 발생하는 tenor를 추가함.  


// C matrix = base tenor (16) X cf tenor (102) 
// 이자지급주기마다 1원 주는 경우 = 1+(ytm/freq)
  this.cfMatrix = new double[this.tenor.length][this.cfCol.length]; 
  
  for(int i=0; i<cfMatrix.length; i++) {
    for(int j=0; j<cfMatrix[i].length; j++) {
      if(Math.abs(this.cfCol[j] - this.tenor[i]) < ZERO_DOUBLE) {					
        this.cfMatrix[i][j] = 1 + this.iRateBase[i] / this.freq;
      }
      else if(this.cfCol[j] < this.tenor[i]) {
        int tmp = (int) ((this.tenor[i] - this.cfCol[j]) * MONTH_IN_YEAR) % (MONTH_IN_YEAR / this.freq);
        if(tmp == 0) this.cfMatrix[i][j] = this.iRateBase[i] / this.freq;
        else  this.cfMatrix[i][j] = 0.0;
      }
      else this.cfMatrix[i][j] = 0.0;				
    }
  }
```
{% endcode %}

* ytm
  * \[0.03302, 0.03583, 0.03681, 0.03686, 0.03757, 0.03795, 0.03651, 0.03642, 0.0374, 0.0367, 0.03703, 0.0366, 0.0368, 0.03685, 0.03665, 0.0367]
* cf tenor&#x20;
  * \[0.25, 0.5, 0.75, 1.0, 1.5, 2.0, 2.5, 3.0, 3.5, 4.0, 4.5, 5.0,
* cf matrix&#x20;
  * 0.25 \[1.01651, 0.0, 0.0, 0.0, 0.0, ...  => 1.01651 = 1+0.03302/2
  * 0.5   \[0.0, 1.017915, 0.0, 0.0, 0.0, ... => 1.017915 = 1+0.03583/2
  * 0.75 \[0.018405, 0.0, 1.018405, 0.0, ... => 1.018405 = 1+0.03681/2

</details>

<details>

<summary>2.1.2. cfColSet @ 이자지급주기 =0 </summary>

만기에 단일 현금흐름이 발생하는 경우 cfMatrix

{% code title="else (freq == 0)" %}
```java
  this.cfCol = this.tenor;
  this.cfMatrix = new double[this.tenor.length][this.cfCol.length];
  
  for(int i=0; i<cfMatrix.length; i++) {
    for(int j=0; j<cfMatrix[i].length; j++) {
      if(i == j) {
        this.cfMatrix[i][j] = 1.0; // 만기와 같은 시점에만 1원을 발생시킴 
      }
      else {
        this.cfMatrix[i][j] = 0.0;
      }									
    }
```
{% endcode %}

</details>

<details>

<summary>2.1.3 mean</summary>

{% code title="mean" %}
```java
// Constructing m, mu, m - C * mu
double[] mean = new double[this.tenor.length];
for(int i=0; i<mean.length; i++) 
    mean[i] = this.ytmPrice(this.tenor[i], this.iRateBase[i], this.freq);
 
```
{% endcode %}

i번째 asset의 가격 $$m_i$$ 을 위에서 정의한 현가함수로 평가하면  $$p(t)=m_i =  \displaystyle\sum_{j=1}^Mc_{i,j} \cdot v(0,t_j)$$&#x20;

* mean = \[1.0081874129064574, 1.0, 1.0092819049670276, 1.0, 0.9999999999999998, 1.0000000000000002, 1.0000000000000004, 0.9999999999999998, 1.0000000000000007, 0.999999999999999, 0.9999999999999991, 1.0000000000000002, 1.0000000000000002, 1.0000000000000024, 1.0000000000000024, 0.9999999999999958]

{% code title=" mean[i]" %}
```java
private double ytmPrice(double tenor, double ytm, int freq) {
  
  if(freq < 1) return 1 / Math.pow(1 + ytm, tenor);
  
  double T  = tenor;
  double P  = 0.0;
  double Cf = 0.0;
  double Df = 0.0;		
  
  while(T > 0) {
    if(Math.abs(T - tenor) < ZERO_DOUBLE) Cf = 1 + ytm / freq;
    else Cf = ytm / freq;
    
    if(Math.abs(T * freq - (int) (T * freq)) < ZERO_DOUBLE) 
         Df = Math.pow(1 + ytm / freq, -T * freq);
    else Df = 1 / (1 + ytm * T);
    
    P += Cf * Df;
    T -= 1.0 / freq;
  }		
  return P;
}
```
{% endcode %}

</details>

<details>

<summary>2.1.4 muCol ; <span class="math">\mu </span> <mark style="color:red;">수식은 중복되지만 job에 따라 tenor 가 다름</mark> </summary>

{% code title="muCol" %}
```java
double[] muCol = new double[this.cfCol.length]; //CF Tenor 단위로 생성 
for(int i=0; i<muCol.length; i++) 
    muCol[i] = this.zeroBondUnitPrice(this.ltfrCont, this.cfCol[i]);
```
{% endcode %}

전체 프로젝션 기간까지  UFR로 할인한 현가 산출.

$$\mu = \begin{bmatrix}e^{-UFR\cdot t_1} \\e^{-UFR\cdot t_2} \\ \vdots\\e^{-UFR\cdot t_j}\\\vdots\\ e^{-UFR\cdot t_N} \end{bmatrix}$$

* \[0.9910298263929155, 0.9821401168003722, 0.9733301494461906, 0.9645992090286486, 0.9473715798209436, 0.9304516340586946, 0.9138338765515034, 0.8975129102524304, 0.881483434505164, ... 0.19052741157996403, 0.18712461426281846, 0.18378259050830908, 0.18050025490770566, 0.17727654143755098, 0.17411040311344236, 0.17100081164999617, 0.16794675712688567, 0.16494724766084323]

</details>

<details>

<summary>2.1.5 wilsonFn  <mark style="color:red;">중복되지만 순서상 어쩔수 없음</mark>  </summary>

{% code title="weight; wilsonFn" %}
```java
RealMatrix weight  = MatrixUtils.createRealMatrix(
    (this.cfCol, this.cfCol, alpha, this.ltfrCont));
```
{% endcode %}

$$= e^{-UFR \cdot(t+u_j)} \cdot \{\alpha\cdot min(t,u_j)-0.5\cdot e^{-\alpha \cdot max(t,u_j)} \cdot (e^{\alpha \cdot min(t,u_j)} - e^{-\alpha \cdot min(t,u_j)}) \}$$

{% code title="smithWilsonWeight()" %}
```java
// wilson function 
private double[][] smithWilsonWeight(
    double[] prjYearFrac
  , double[] tenorYearFrac
  , double alpha
  , double ltfrCont) 

{  
  double[][] weight = new double[prjYearFrac.length][tenorYearFrac.length];
  double min, max;
  
  for(int i=0; i<prjYearFrac.length; i++) {
    for(int j=0; j<tenorYearFrac.length; j++) {
      
      min = Math.min(prjYearFrac[i], tenorYearFrac[j]);
      max = Math.max(prjYearFrac[i], tenorYearFrac[j]);
      weight[i][j] = Math.exp(-ltfrCont * (prjYearFrac[i] + tenorYearFrac[j])) * (alpha * min - Math.exp(-alpha*max) * Math.sinh(alpha*min));				
    }
  }
  return weight;
}
```
{% endcode %}

</details>

<details>

<summary>2.1.6. zeta </summary>

{% code title="cfMatx" %}
```java
RealMatrix cfMatx  = MatrixUtils.createRealMatrix(this.cfMatrix);
```
{% endcode %}

{% code title="(CWCt)^(-1)" %}
```java
RealMatrix cwctInv = 
    MatrixUtils.inverse(
                cfMatx
                .multiply(weight)
                .multiply(cfMatx.transpose())
                );
```
{% endcode %}

{% code title="C * Mu" %}
```java
RealMatrix cDotMu  = 
    cfMatx.multiply(MatrixUtils.createColumnRealMatrix(muCol)); 
```
{% endcode %}

{% code title="m - C * mu" %}
```java
RealMatrix mSubCU  = 
    MatrixUtils.createColumnRealMatrix(mean).subtract(cDotMu); 
```
{% endcode %}

{% code title="zeta" %}
```java
// zeta = (CWCt)^(-1) * ( m - C * Mu) 
RealMatrix zetaCol = cwctInv.multiply(mSubCU); 
```
{% endcode %}



</details>

<details>

<summary>2.1.7. zetaHat</summary>

{% code title="zetaHat" %}
```java
//C^T * zeta
this.zetaHat  = cfMatx.transpose().multiply(zetaCol);  
```
{% endcode %}



</details>

### 2.2. 만기별 가중치 (wilson function)&#x20;

{% code title="2.2. 만기별 가중치 " %}
```java
  RealMatrix weightPrj = MatrixUtils.createRealMatrix(
      smithWilsonWeight(
            prjTenor
          , this.cfCol
          , alpha
          , this.ltfrCont)
  );

```
{% endcode %}

<details>

<summary><span class="math">W(t, u_j)</span> ; smithWilsonWeight()</summary>

$$= e^{-UFR \cdot(t+u_j)} \cdot \{\alpha\cdot min(t,u_j)-0.5\cdot e^{-\alpha \cdot max(t,u_j)} \cdot (e^{\alpha \cdot min(t,u_j)} - e^{-\alpha \cdot min(t,u_j)}) \}$$

```java
// wilson function 
private double[][] smithWilsonWeight(
    double[] prjYearFrac
  , double[] tenorYearFrac
  , double alpha
  , double ltfrCont) 

{  
  double[][] weight = new double[prjYearFrac.length][tenorYearFrac.length];
  double min, max;
  
  for(int i=0; i<prjYearFrac.length; i++) {
    for(int j=0; j<tenorYearFrac.length; j++) {
      
      min = Math.min(prjYearFrac[i], tenorYearFrac[j]);
      max = Math.max(prjYearFrac[i], tenorYearFrac[j]);
      weight[i][j] = Math.exp(-ltfrCont * (prjYearFrac[i] + tenorYearFrac[j])) * (alpha * min - Math.exp(-alpha*max) * Math.sinh(alpha*min));				
    }
  }
  return weight;
}
```

</details>

### 2.3. Mu 산출&#x20;

{% code title="프로젝션 기간 UFR로 할인한 현가 산출." %}
```java
double[] muPrj = new double[prjTenor.length];
for(int i=0; i<muPrj.length; i++) 
  muPrj[i] = this.zeroBondUnitPrice(this.ltfrCont, prjTenor[i]);
```
{% endcode %}

<details>

<summary><span class="math">\mu </span> ; Mu </summary>

전체 프로젝션 기간(base tenor)  UFR로 할인한 현가 산출.

$$\mu = \begin{bmatrix}e^{-UFR\cdot t_1} \\e^{-UFR\cdot t_2} \\ \vdots\\e^{-UFR\cdot t_j}\\\vdots\\ e^{-UFR\cdot t_N} \end{bmatrix}$$

</details>

### 2.4. zero coupon bond (현가함수)

{% code title="2.4. zcb 가격 산출" %}
```java
RealMatrix priceCol  
= weightPrj.multiply(this.zetaHat).add(MatrixUtils.createColumnRealMatrix(muPrj));
  //p(t) = e^(-ufr*t) + sigma (zetaHat * W) s.t. W = wilson-function 
```
{% endcode %}



### 2.5. spot rate, fwd rate ,swResult&#x20;

{% code title="spotCont, fwdCont 산출 & swResult출력 " %}
```java
  double[] priceZcb = new double[prjTenor.length];
  double[] spotCont = new double[prjTenor.length];
  double[] fwdCont  = new double[prjTenor.length];

  for(int i=0; i<prjTenor.length; i++) {
    //zero coupon bond price
    priceZcb[i] = priceCol.getEntry(i,0);	
    spotCont[i] = -1.0 / prjTenor[i] * Math.log(priceZcb[i]); 
    spotCont[i] = Math.log(Math.exp(spotCont[i]) + this.liqPrem);
    
    // 직전 tenor spot rate와 당기 tenor spot rate를 이용해서 fwd rate산출 
    fwdCont[i]  = (i > 0) ? 
           (spotCont[i] * prjTenor[i] - spotCont[i-1] * prjTenor[i-1])
            / (prjTenor[i] - prjTenor[i-1]) 
          : spotCont[i];
    
    SmithWilsonRslt swResult = new SmithWilsonRslt();
    
    swResult.setBaseDate(baseDate.toString());
    swResult.setResultType("SW SPOT");
    swResult.setScenType("1");			
    swResult.setMatCd(String.format("%s%04d", TIME_UNIT_MONTH, (int) (prjTenor[i] * MONTH_IN_YEAR)));
    swResult.setDcntFactor(priceZcb[i]);
    swResult.setSpotCont(round(spotCont[i]));
    swResult.setFwdCont(round(fwdCont[i]));
    swResult.setSpotDisc(round(irContToDisc(spotCont[i])));			
    swResult.setFwdDisc(round(irContToDisc(fwdCont[i])));
    
    swResultlList.add(swResult);
  }
  return swResultlList;
```
{% endcode %}

