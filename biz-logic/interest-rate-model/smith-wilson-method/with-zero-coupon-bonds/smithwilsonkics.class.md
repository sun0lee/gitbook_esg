# SmithWilsonKics.class

## 1. 객체 생성 ; 초기화 및 tenor 설정 &#x20;

{% code overflow="wrap" %}
```java
// 객체 생성 (input)
SmithWilsonKics swKics = new SmithWilsonKics
(
    baseDate
  , irCurveSpotList
  , CMPD_MTD_DISC //cmpdType
  , true //isRealNumber
  , swSce.getValue().getLtfr()   //ltfr
  , swSce.getValue().getLtfrCp() //ltfrT ; 60
  , projectionYear //prjYear ; 120 
  , 1 //prjYear
  , 100 //alphaItrNum
  , DCB_MON_DIF //dayCountBasis ; 9 
);				
```
{% endcode %}

```java
// irModel 초기화 및 기본설정 (프로젝션 기간 , tenor 등 )
public SmithWilsonKics(LocalDate baseDate
        , List<IrCurveSpot> irCurveHisList
        , char cmpdType
        , boolean isRealNumber
        , double ltfr
        , int ltfrT
        , int prjYear
        , int prjInterval
        , int alphaItrNum
        , int dayCountBasis) 
{				
  super(); // irModel 초기화 
  this.baseDate = baseDate;		
  this.setTermStructureBase(irCurveHisList);
  this.setLastLiquidPoint(this.tenor[this.tenor.length-1]);
  this.cmpdType = cmpdType; // D
  this.isRealNumber = isRealNumber;
  this.ltfr = ltfr;
  this.ltfrT = ltfrT; // 60 ; alpha를 구하기 위한 최초 수렴시점.
  this.prjYear = prjYear;	// 120	
  this.prjInterval = prjInterval; // 1
  this.alphaItrNum = alphaItrNum; // 100 : alpha를 구하기 위한 반복횟수. 
  this.dayCountBasis = dayCountBasis; // 9
  this.setSwAttributes();
  this.setProjectionTenor();
}
```

### 1.1.tenor 및 LTFR&#x20;

```java
setSwAttributes()

this.tenorDate     = new LocalDate[this.tenor.length];
this.tenorYearFrac = new double[this.tenor.length];
int yearToMonth    = (this.timeUnit == TIME_UNIT_YEAR) ? 12 : 1;

double toRealScale = this.isRealNumber ? 1.0 : 0.01;
this.ltfrCont = irDiscToCont(toRealScale * this.ltfr);

//tenor별 실제 날짜 
for(int i=0; i<this.tenor.length; i++) {	
  this.tenorDate[i] = this.baseDate.plusMonths((long) Math.round(this.tenor[i] * yearToMonth));
  this.tenorYearFrac[i] = getTimeFactor(this.baseDate,  this.tenorDate[i],  this.dayCountBasis);
  this.iRateBase[i] = (this.cmpdType == CMPD_MTD_DISC) ? irDiscToCont(toRealScale*this.iRateBase[i]) : toRealScale*this.iRateBase[i];
}
```

* tenorDate&#x20;
  * \[2023-03-31, 2023-06-30, 2023-09-30, 2023-12-31, 2024-06-30, 2024-12-31, 2025-06-30, 2025-12-31, 2026-12-31, 2027-12-31, 2029-12-31, ...l]
* tenorYearFrac&#x20;
  * \[0.25, 0.5, 0.75, 1.0, 1.5, 2.0, 2.5, 3.0, 4.0, 5.0, 7.0, ...]
*   ltfrCont = irDiscToCont(toRealScale \* this.ltfr);

    * Math.log(1.0 + discRate);



### 1.2. projection 기간 tenor 설정&#x20;

```java
setProjectionTenor()

int monthToYear = (this.prjTimeUnit == TIME_UNIT_MONTH) ? 12 : 1;
int yearToMonth = (this.prjTimeUnit == TIME_UNIT_YEAR)  ? 12 : 1;
int prjNum      = this.prjYear * monthToYear / ((this.prjInterval > 0) ? this.prjInterval : 1);

this.prjDate      = new LocalDate[prjNum + 1];
this.prjYearFrac  = new double[prjNum + 1];
// projection 날짜 별 
for(int i=0; i<this.prjDate.length; i++) {
  
  this.prjDate[i] = this.baseDate.plusMonths((long) Math.round((i+1) * this.prjInterval * yearToMonth));
  this.prjYearFrac[i] = getTimeFactor(this.baseDate,  this.prjDate[i],  this.dayCountBasis);
}
```

* prjDate
  * \[2023-01-31, 2023-02-28, 2023-03-31, 2023-04-30, 2023-05-31, 2023-06-30, ...]
*   prjYearFrac

    * \[0.08333333333333333, 0.16666666666666666, 0.25, 0.3333333333333333, 0.4166666666666667, 0.5...]



## 2.smith-wilson Result 생성&#x20;

```java
private List<SmithWilsonRslt> swProjectionList() {

List<SmithWilsonRslt> swResultlList = new ArrayList<SmithWilsonRslt>();	
// 2.1. alpha 찾기 
this.smithWilsonAlphaFinding();
log.info("AlphaOpt: {}, Error: {}", this.alphaApplied, Math.abs(this.alphaFwd - this.ltfrCont));

double[] df = new double[this.prjYearFrac.length];

// 2.2. 만기별 zcb 가격함수 생성 
for(int i=0; i<df.length; i++) df[i] = zeroBondUnitPrice(this.ltfrCont,  this.prjYearFrac[i]);

RealMatrix weightPrjTenor = MatrixUtils.createRealMatrix(smithWilsonWeight(this.prjYearFrac, this.tenorYearFrac, this.alphaApplied, this.ltfrCont));		
RealMatrix dfCol = MatrixUtils.createColumnRealMatrix(df);
RealMatrix sigmaCol = weightPrjTenor.multiply(this.zetaColumn);
RealMatrix priceCol = dfCol.add(sigmaCol);

// 2.3. 만기별 할인율 생성  
double[] priceZcb = new double[this.prjYearFrac.length];
double[] spotCont = new double[this.prjYearFrac.length];
double[] fwdCont  = new double[this.prjYearFrac.length];	

for(int i=0; i<this.prjYearFrac.length-1; i++) {			
	priceZcb[i] = priceCol.getEntry(i,0);
	spotCont[i] = -1.0 / this.prjYearFrac[i] * Math.log(priceZcb[i]);
	fwdCont[i]  = (i > 0) ? (spotCont[i] * prjYearFrac[i] - spotCont[i-1] * prjYearFrac[i-1]) / (prjYearFrac[i] - prjYearFrac[i-1]) : spotCont[i];
	
	SmithWilsonRslt swResult = new SmithWilsonRslt();
	
	swResult.setBaseDate(baseDate.toString());
	swResult.setResultType("Smith-Wilson");
	swResult.setScenType("1");
	swResult.setMatCd(String.format("%s%04d",  this.prjTimeUnit, (i+1) * this.prjInterval ));
	swResult.setMatTerm(round(this.prjYearFrac[i]));
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



### 2.1. Alpha ;  `smithWilsonAlphaFinding()`

#### 2.1.1. Alpha 값 조정&#x20;

```java
for(int i=0; i<this.alphaItrNum; i++) {

if(i==0) {
	this.alphaApplied  = 0.0001; // alpha 초기값 
	this.alphaDApplied = (1.0 - 0.0001) / 4.0;
}			
if(i==1) {
	this.alphaApplied  = (1.0 + 0.0001) / 2.0;
}

RealMatrix tenorCol = MatrixUtils.createColumnRealMatrix(this.tenorYearFrac);
```

#### 2.1.2. 만기별 가중치 wilson function&#x20;

```java
// 조정된 alpha 값으로 W, W^(-1),zeta 재산출  
// 2.1.1  W = wilson function : 만기별 가중치 
RealMatrix weight   = MatrixUtils.createRealMatrix(
    smithWilsonWeight
        ( this.tenorYearFrac
        , this.tenorYearFrac
        , this.alphaApplied
        , this.ltfrCont)
    );
// 2.1.2. W^(-1)
RealMatrix invWeight = MatrixUtils.inverse(weight);
```

<details>

<summary><span class="math">W(t, u_j)</span> ; smithWilsonWeight()</summary>

$$= e^{-UFR \cdot(t+u_j)} \cdot \{\alpha\cdot min(t,u_j)-0.5\cdot e^{-\alpha \cdot max(t,u_j)} \cdot (e^{\alpha \cdot min(t,u_j)} - e^{-\alpha \cdot min(t,u_j)}) \}$$

```java
// wilson function 
private double[][] smithWilsonWeight(double[] prjYearFrac, double[] tenorYearFrac, double alpha, double ltfrCont) {
  
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

#### 2.1.3. zeta 산출  ; $$\zeta = W^{-1}\cdot(P-\mu)$$

```java
double[] pVal = new double[this.tenorYearFrac.length];
double[] mean = new double[this.tenorYearFrac.length];
double[] loss = new double[this.tenorYearFrac.length];
double[] sinh = new double[this.tenorYearFrac.length];

for(int j=0; j<loss.length; j++) {
  // P
  pVal[j] = zeroBondUnitPrice(this.iRateBase[j], this.tenorYearFrac[j]);
  // Mu
  mean[j] = zeroBondUnitPrice(this.ltfrCont, this.tenorYearFrac[j]);
  // P -Mu
  loss[j] = smithWilsonLoss(this.iRateBase[j], this.tenorYearFrac[j], this.ltfrCont);
  sinh[j] = Math.sinh(this.alphaApplied * this.tenorYearFrac[j]);
}

// P - Mu
RealMatrix lossCol = MatrixUtils.createColumnRealMatrix(loss);
// zeta = W^(-1)*(P - Mu) 
RealMatrix zetaCol = invWeight.multiply(lossCol);		
RealMatrix sinhCol = MatrixUtils.createColumnRealMatrix(sinh);
RealMatrix qMatDiag = MatrixUtils.createRealDiagonalMatrix(mean); //대각행렬
```

#### 2.1.3.  alpha 제약조건 확인&#x20;

최초 수렴시점(60y)의 선도금리와 장기목표금리 간의 차이가 1.0bp이내가 되도록 하는 최소값

```java
double kappaNum = tenorCol.transpose().multiply(qMatDiag).multiply(zetaCol).scalarMultiply(this.alphaApplied).scalarAdd(1.0).getEntry(0,0);
double kappaDenom = sinhCol.transpose().multiply(qMatDiag).multiply(zetaCol).getEntry(0,0);
this.kappaApplied = kappaNum / (Math.abs(kappaDenom) < ZERO_DOUBLE ? 1.0 : kappaDenom);

this.alphaPp  = Math.exp(-this.ltfrCont * this.ltfrT) * (kappaNum - Math.exp(-this.alphaApplied * this.ltfrT) * kappaDenom);
this.alphaDpp = -this.ltfrCont * this.alphaPp + Math.exp(-this.ltfrCont * this.ltfrT) * this.alphaApplied * Math.exp(-this.alphaApplied * this.ltfrT) * kappaDenom;
// ltfrT;60Y의 alpha 적용한 fwdrate
this.alphaFwd = -1 / this.alphaPp * this.alphaDpp;		

this.zetaColumn = zetaCol;

// 초회 alpha검토 
if(i==0) {
  if(Math.abs(Math.exp(this.ltfrCont) - Math.exp(this.alphaFwd)) < ltfrEpsilon) {
    break;
  }
  else if(this.alphaFwd > this.ltfrCont) {
    this.alphaFwdT = Math.log(Math.exp(this.ltfrCont) + ltfrEpsilon);
  }
  else {
    this.alphaFwdT = Math.log(Math.exp(this.ltfrCont) - ltfrEpsilon);
  }
}

// 이후 회차 alpha 검토 
else {
  if(this.alphaFwdT < this.ltfrCont) {
    if(this.alphaFwd < this.alphaFwdT) {
      this.alphaApplied = this.alphaApplied + this.alphaDApplied;
    }
    else {
      this.alphaApplied = this.alphaApplied - this.alphaDApplied;
    }					
  }
  else {
    if(this.alphaFwd < this.alphaFwdT) {
      this.alphaApplied = this.alphaApplied - this.alphaDApplied;
    }
    else {
      this.alphaApplied = this.alphaApplied + this.alphaDApplied;
    }					
  }
  this.alphaDApplied *= 0.5;	
}
}
```

### 2.2. 만기별 zero coupon bond 가격 산출  (=discount factor)

```java
// Mu : 프로젝션 기간까지 월별 버킷단위로 Mu 산출 
double[] df = new double[this.prjYearFrac.length];
for(int i=0; i<df.length; i++) 
    df[i] = zeroBondUnitPrice(this.ltfrCont,this.prjYearFrac[i]);

// W (확정된 alpha를 적용한 wilson function)
RealMatrix weightPrjTenor = MatrixUtils.createRealMatrix(smithWilsonWeight(this.prjYearFrac, this.tenorYearFrac, this.alphaApplied, this.ltfrCont));		
// Mu
RealMatrix dfCol = MatrixUtils.createColumnRealMatrix(df);		
// W*zeta / zeta는 findingAlpha에서 이미 구함. 
RealMatrix sigmaCol = weightPrjTenor.multiply(this.zetaColumn); 
// P = Mu + W*zeta ; 모든 만기별 가격
RealMatrix priceCol = dfCol.add(sigmaCol);	
```

<details>

<summary><span class="math">\mu </span> ; Mu </summary>

전체 프로젝션 기간 1M\~1440M 까지  UFR로 할인한 현가 산출.

$$\mu = \begin{bmatrix}e^{-UFR\cdot t_1} \\e^{-UFR\cdot t_2} \\ \vdots\\e^{-UFR\cdot t_j}\\\vdots\\ e^{-UFR\cdot t_N} \end{bmatrix}$$

</details>

<details>

<summary><span class="math">W(t, u_j)</span> smithWilsonWeight()</summary>

$$= e^{-UFR \cdot(t+u_j)} \cdot \{\alpha\cdot min(t,u_j)-0.5\cdot e^{-\alpha \cdot max(t,u_j)} \cdot (e^{\alpha \cdot min(t,u_j)} - e^{-\alpha \cdot min(t,u_j)}) \}$$

```java
// wilson function 
private double[][] smithWilsonWeight(double[] prjYearFrac, double[] tenorYearFrac, double alpha, double ltfrCont) {
  
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



### 2.3. S-W 결과 setter&#x20;

```java
double[] priceZcb = new double[this.prjYearFrac.length];
double[] spotCont = new double[this.prjYearFrac.length];
double[] fwdCont  = new double[this.prjYearFrac.length];	

for(int i=0; i<this.prjYearFrac.length-1; i++) {			
	priceZcb[i] = priceCol.getEntry(i,0);
	spotCont[i] = -1.0 / this.prjYearFrac[i] * Math.log(priceZcb[i]);
	fwdCont[i]  = (i > 0) ? (spotCont[i] * prjYearFrac[i] - spotCont[i-1] * prjYearFrac[i-1]) / (prjYearFrac[i] - prjYearFrac[i-1]) : spotCont[i];
	
	SmithWilsonRslt swResult = new SmithWilsonRslt();
	
	swResult.setBaseDate(baseDate.toString());
	swResult.setResultType("Smith-Wilson");
	swResult.setScenType("1");
	swResult.setMatCd(String.format("%s%04d",  this.prjTimeUnit, (i+1) * this.prjInterval ));
	swResult.setMatTerm(round(this.prjYearFrac[i]));
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

<details>

<summary>priceZcb -> spotCont</summary>

만기 t 시점의 1원의 현재가치는 연속복리이율 r로 표현하면 $$p = 1\cdot e^{-r\cdot t}$$ 이므로&#x20;

이 식을 _r_ 에 대해 정리하면 $$r = -ln(p) / t$$

</details>

