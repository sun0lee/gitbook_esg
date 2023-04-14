# AFNelsonSiegel

[calculate-shock-spread.md](../../esg-process/2.-adjusted-risk-free-term-structure/ir-shock-spread/calculate-shock-spread.md "mention")

[job220](../../../etc/java/src/job220/ "mention")

[esg220\_shksprdafns.md](../../../etc/java/src/job220/esg220\_shksprdafns.md "mention")



## 0. 생성자&#x20;

```java
AFNelsonSiegel afns = new AFNelsonSiegel
                        ( bssd
                        , curveHisList
                        , curveBaseList
                        , irModelMst
                        , irParamSw
                        , argInDBMap
) ;
```

<details>

<summary>인스턴스 생성  &#x26;  변수 초기화 </summary>

```java
public AFNelsonSiegel( String bssd
 , List<IRateInput> iRateHisList
 , List<IRateInput> iRateBaseList
 , IrParamModel irModelMst 
 , IrParamSw    irParamSw  
 , Map<String, String>  argInDBMap ) 

{		
  // 모형에서 계산에 필요한 초기인수는 필요한 곳에서 풀어헤치기 
  double errorTolerance                 = irModelMst.getItrTol();
  double ltfr                           = irParamSw.getLtfr();
  int    ltfrT                          = irParamSw.getLtfrCp();
  
  double confInterval    = Double. valueOf((String) argInDBMap.getOrDefault("AFNS_CONF_INTERVAL"   , "0.995"));
  int    kalmanItrMax    = Integer.valueOf((String) argInDBMap.getOrDefault("AFNS_KALMAN_ITR_MAX"  , "100"));			
  double sigmaInit       = Double. valueOf((String) argInDBMap.getOrDefault("AFNS_SIGMA_INIT"      , "0.05"));
  double epsilonInit     = Double. valueOf((String) argInDBMap.getOrDefault("AFNS_EPSILON_INIT"    , "0.001"));	
  int    prjYear         = Integer.valueOf((String) argInDBMap.getOrDefault("PROJECTION_YEAR"      , "120"));
  
  
  this.baseDate      =  IrModel.stringToDate(bssd);
  this.mode          =  irModelMst.getIrModelNm() ;
  this.inputParas    =  null;		
  this.setTermStructureHis(iRateHisList, iRateBaseList);
  this.isRealNumber  = true;
  this.cmpdType      = 'D';		
  this.dt            = 1.0 /52.0 ;	//weekly only
  this.initSigma     = sigmaInit;
  this.dayCountBasis = DCB_MON_DIF;
  this.ltfrL         = ltfr;
  this.ltfrA         = 0;
  this.ltfrT         = ltfrT;
  this.liqPrem       = 0.0;
  this.term          = 1.0 / 12;
  this.minLambda     = 0.05;
  this.maxLambda     = 2.0;
  this.nf            = 3;
  this.prjYear       = prjYear;
  this.accuracy      = errorTolerance;
  this.itrMax        = kalmanItrMax;
  this.confInterval  = confInterval;
  this.epsilon       = epsilonInit;
  this.setIrmodelAttributes();
}

```

</details>



## 1. getAfnsResultList()

* AFNS 모형의 초기 모수 설정.
* kalmanFiltering을 이용하여 모수 최적화.&#x20;
* 추정된 모수 기반으로 충격시나리오 산출.

```java
public List<IrDcntSceDetBiz> getAfnsResultList() {

if(!this.optParasFlag) {
  // Initializing AFNS Parameter
  initializeAfnsParas();
    
    // Determine this.initParas
    if(this.inputParas != null) this.initParas = this.inputParas;  
      
  // To set this.optParas, this.optLSC
  kalmanFiltering();
 }
  
  // To set this.IntShock
  afnsShockGenerating();	
  
  return this.rsltList;
}
```



### 1.1. initializeAfnsParas()

<details>

<summary>initializeAfnsParas()</summary>

```java
  findInitialLambda();
  findInitailThetaKappa();
  
  this.initParas = new double[14];
  
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



```java
protected void findInitialLambda() {
  
  UnivariateFunction fp = new UnivariateFunction() {
    public double value(double lambda) {
      return residualSumOfSquares(lambda);
    }
  };
  
  UnivariateOptimizer optimizer = new BrentOptimizer(1e-10, 1e-14);
  this.lambda = optimizer.optimize
        ( new MaxEval(10000)
        , new UnivariateObjectiveFunction(fp)
        , GoalType.MINIMIZE
        , new SearchInterval(this.minLambda, this.maxLambda)).getPoint();
}
```

```java
private void findInitailThetaKappa() {	
  
  SimpleRegression linRegL = new SimpleRegression(true);
  SimpleRegression linRegS = new SimpleRegression(true);
  SimpleRegression linRegC = new SimpleRegression(true);
      
  for(int i=0; i<coeffLt.length-1; i++) {
    
    linRegL.addData(coeffLt[i], coeffLt[i+1]);
    linRegS.addData(coeffSt[i], coeffSt[i+1]);
    linRegC.addData(coeffCt[i], coeffCt[i+1]);
  }				
  
  this.thetaL = linRegL.getIntercept() / (1.0 - linRegL.getSlope());
  this.thetaS = linRegS.getIntercept() / (1.0 - linRegS.getSlope());
  this.thetaC = linRegC.getIntercept() / (1.0 - linRegC.getSlope());
  
  this.kappaL = -Math.log(linRegL.getSlope()) / this.dt;
  this.kappaS = -Math.log(linRegS.getSlope()) / this.dt;
  this.kappaC = -Math.log(linRegC.getSlope()) / this.dt;
}
```

</details>

IrParamAfnsBiz에 모수가 이미 설정된 경우 초기화된 모수를 적용하는 대신 입력된 값을 모수로 설정함.&#x20;

```java
// Determine this.initParas
if(this.inputParas != null) this.initParas = this.inputParas; 

// this.inputParas : 
// IrParamAfnsBiz에 입력된 모수가 있는 경우 초기화된 모수대신 입력된 모수를 우선 사용하도록 함. 
```

### 1.2. kalmanFiltering()

초기 모수를 이용하여 뭔가 최적화 과정을 통해 모수를 추정하는 과정 ?? 암튼 이걸 통해서 AFNS 모형의 모수가  결정된다.&#x20;

<details>

<summary>kalmanFiltering(this.initParas)</summary>

* 모수를 단순히 double 배열로 정의해서 idx에 따라 처리하고 있음. ( double\[] inputParas )
* TODO : 각 idx별 의미하는 모수값을 enum으로 정의하기&#x20;
  * 방법 자체를 이해하면 모수를 정의할때 고려할 사항을 알 수 있을텐데.. 배경을 살펴보기 &#x20;

```java
private void kalmanFiltering(double[] paras) {
  
  MultivariateFunction fp = new MultivariateFunction() {
    public double value(double[] inputParas) {
      return logLikelihood(inputParas);
    }
  };		
  
  double[] fpLower = new double[paras.length];
  double[] fpUpper = new double[paras.length];
  double[] fpScale = new double[paras.length];
  
  for(int i=0; i<paras.length; i++) {
    if(i == 0) {  //lambda
      fpLower[i] = this.minLambda;
      fpUpper[i] = this.maxLambda;				
    }
    else if(i < 4) {  // theta(no constraint)
      fpLower[i] = -100000.0;
      fpUpper[i] = 100000.0;				
    }
    else if(i < 7) {  // kappa 
      fpLower[i] = 1E-4;
      fpUpper[i] = 100000.0;
    }
    else if(i < 13) { // sigma(diagonal only)
      if(i == 7 || i == 9 || i == 12) {
        fpLower[i] = 1E-6;
        fpUpper[i] = 1.0;
      }
      else {
        fpLower[i] = -1.0;
        fpUpper[i] = 1.0;
      }				
    }
    else { // epsilon
      fpLower[i] = 0.0;
      fpUpper[i] = 1000000;
    }			
    fpScale[i] = 1000;
  }		
    MultivariateFunctionPenaltyAdapter fpConstr = 
    new MultivariateFunctionPenaltyAdapter( fp
                                          , fpLower
                                          , fpUpper
                                          , 1000
                                          , fpScale);
  
  double[] calibParas = paras;
  double   calibValue = 0.0;	
  
  log.info("{}, {}, {}", LocalDateTime.now(), paras);		
  try {			
    for(int i=0; i<this.itrMax; i++) {		
      
      SimplexOptimizer optimizer = new SimplexOptimizer(1e-12, 1e-12);
      AbstractSimplex  ndsimplex = new NelderMeadSimplex(
                                    nelderMeadStep(calibParas, 0.001));
      PointValuePair   result    = optimizer.optimize
                                      (new MaxEval(100000)
                                     , new ObjectiveFunction(fpConstr)
                                     , ndsimplex
                                     , GoalType.MINIMIZE
                                     , new InitialGuess(calibParas));

      log.info("{}, {}, {}, {}"
            , i
            , result.getValue()
            , LocalDateTime.now()
            , result.getPoint()
              );			

      if(Math.abs(result.getValue() - calibValue) < this.accuracy) break;
      calibParas   = constraints(result.getPoint());
      calibValue   = result.getValue();	
    }
    
    this.optParas = calibParas;			
    this.optLSC   = new double[] 
    {
      this.coeffLt[this.iRateHis.length-1]
    , this.coeffSt[this.iRateHis.length-1]
    , this.coeffCt[this.iRateHis.length-1]
    };
  }
  catch (Exception e) {
    log.error("Error in finding Kalman Gain in AFNS module");
    e.printStackTrace();
  }		
  log.info("{}, {}, {}", LocalDateTime.now(), this.optLSC, this.optParas);
}
```

</details>

### 1.3. afnsShockGenerating()

<details>

<summary>afnsShockGenerating()</summary>

```java
private void afnsShockGenerating() {
  
  double       Lambda     = this.optParas[0];
  SimpleMatrix Theta      = new SimpleMatrix(vecToMat(new double[] {this.optParas[1], this.optParas[2], this.optParas[3]}));
  SimpleMatrix Kappa      = new SimpleMatrix(toDiagMatrix(this.optParas[4], this.optParas[5], this.optParas[6]));
  SimpleMatrix Sigma      = new SimpleMatrix(toLowerTriangular3(new double[] {this.optParas[7], this.optParas[8], this.optParas[9], this.optParas[10], this.optParas[11], this.optParas[12]}));
  SimpleMatrix X0         = new SimpleMatrix(vecToMat(new double[] {this.optLSC[0], this.optLSC[1], this.optLSC[2]}));		
  
  
  // AFNS factor loading matrix based on LLP weight
  double[]     tenorLLP   = new double[(int) (Math.round(this.tenor[this.tenor.length-1]))];
  for(int i=0; i<tenorLLP.length; i++) tenorLLP[i] = i+1;
  SimpleMatrix factorLLP  = new SimpleMatrix(factorLoad(Lambda, tenorLLP, true));

  
  // Declare M, N and Calculate NTN | eKappa ~ Kappa^-1 x (I-exp(-Kappa)) x Sigma  |  N ~ W.mat x M  |  NTN ~ t(N) x N
  SimpleMatrix eKappa     = new SimpleMatrix(toDiagMatrix(Math.exp(-Kappa.get(0,0)), Math.exp(-Kappa.get(1,1)), Math.exp(-Kappa.get(2,2))));
  SimpleMatrix IminusK    = new SimpleMatrix(toIdentityMatrix(this.nf)).minus(eKappa);
  SimpleMatrix M          = Kappa.invert().mult(IminusK).mult(Sigma);		
  
  SimpleMatrix N          = new SimpleMatrix(toDiagMatrix(factorLLP.extractMatrix(0, tenorLLP.length, 0, 1).elementSum()
                                                    , factorLLP.extractMatrix(0, tenorLLP.length, 1, 2).elementSum()
                                                    , factorLLP.extractMatrix(0, tenorLLP.length, 2, 3).elementSum())).mult(M);
  SimpleMatrix NTN        = N.transpose().mult(N);   //for IAIS(International Association of Insurance Supervisors) 2017 Field Testing Tech. doc(page 201 of 326)
  
  // Eigen Decomposition & get rotation angle
  Map<Integer, List<Double>> eigVec =  eigenValueUserDefined(NTN, 3);		
  SimpleMatrix Me1        = M.mult(new SimpleMatrix(vecToMat(eigVec.get(0).stream().mapToDouble(Double::doubleValue).toArray())));
  SimpleMatrix Me2        = M.mult(new SimpleMatrix(vecToMat(eigVec.get(1).stream().mapToDouble(Double::doubleValue).toArray())));
  SimpleMatrix S1         = factorLLP.mult(Me1);
  SimpleMatrix S2         = factorLLP.mult(Me2);		
  double rotation         = Math.atan2(S2.elementSum(), S1.elementSum());
  
  // Mean-Reversion, Level and Twist Shock
  SimpleMatrix MeanR      = new SimpleMatrix(toIdentityMatrix(this.nf)).minus(eKappa).mult(Theta.minus(X0));
  SimpleMatrix Level      = new SimpleMatrix(Me1.scale( Math.cos(rotation)).plus(Me2.scale(Math.sin(rotation)))).scale(new NormalDistribution().inverseCumulativeProbability(this.confInterval));
  SimpleMatrix Twist      = new SimpleMatrix(Me1.scale(-Math.sin(rotation)).plus(Me2.scale(Math.cos(rotation)))).scale(new NormalDistribution().inverseCumulativeProbability(this.confInterval));

  SimpleMatrix CoefInt    = new SimpleMatrix(factorLoad(Lambda, this.tenor, true));		                        		
  SimpleMatrix BaseShock  = CoefInt.mult(new SimpleMatrix(vecToMat(new double[] {0.0, 0.0, 0.0})));
  SimpleMatrix MeanRShock = CoefInt.mult(MeanR);
  SimpleMatrix LevelShock = CoefInt.mult(Level);
  SimpleMatrix TwistShock = CoefInt.mult(Twist);
  
  //TODO: To adjust Shock Scale in case of daily history(only in AFNS)
  double levelScale = LevelShock.get(LevelShock.numRows()-1,0) > ZERO_DOUBLE ? 1.0 : -1.0;
  double twistScale = TwistShock.get(TwistShock.numRows()-1,0) > ZERO_DOUBLE ? 1.0 : -1.0;
          
  this.IntShock           = new SimpleMatrix(BaseShock).concatColumns(MeanRShock)
                                                     .concatColumns(LevelShock.scale(+levelScale)).concatColumns(LevelShock.scale(-levelScale))
                                                     .concatColumns(TwistShock.scale(-twistScale)).concatColumns(TwistShock.scale(+twistScale));
  
  this.IntShockName       = new String[] {"1", "2", "3", "4", "5", "6"};
}
```

</details>



## 2. getAfnsParamList()

[#1.2.-kalmanfiltering](afnelsonsiegel.md#1.2.-kalmanfiltering "mention")에서 산출한 모수값을 출력해서 테이블(IR\_PARAM\_AFNS\_CALC)에 적재&#x20;

```java
public List<IrParamAfnsCalc> getAfnsParamList() {

  List<IrParamAfnsCalc> paramList = new ArrayList<IrParamAfnsCalc>();
  
  // TODO : Enum 에서 조건별로 구분하기 
  String[] optParaNames = new String[] 
  {  "LAMBDA"  , "THETA_1" , "THETA_2" , "THETA_3" 
   , "KAPPA_1" , "KAPPA_2" , "KAPPA_3" 
   , "SIGMA_11", "SIGMA_21", "SIGMA_22"
   , "SIGMA_31", "SIGMA_32", "SIGMA_33", "EPSILON" };		
    
  // TODO : enum
  String[] optLSCNames  = new String[] {"L0", "S0", "C0"};        
      
  if(this.optParas != null && this.optLSC != null) {			
    
    for(int i=0; i<this.optParas.length; i++) {
      
      IrParamAfnsCalc param = new IrParamAfnsCalc();
      param.setBaseYymm(dateToString(this.baseDate).substring(0,6));
      param.setIrModelNm(this.mode);
      param.setIrCurveNm(this.irCurveNm);
      param.setParamTypCd(optParaNames[i]);
      param.setParamVal(optParas[i]);
      param.setModifiedBy("GESG_" + this.getClass().getSimpleName());
      param.setUpdateDate(LocalDateTime.now());				
      paramList.add(param);
    }
    
    for(int i=0; i<this.optLSC.length; i++) {

      IrParamAfnsCalc param = new IrParamAfnsCalc();
      param.setBaseYymm(dateToString(this.baseDate).substring(0,6));
      param.setIrModelNm(this.mode);
      param.setIrCurveNm(this.irCurveNm);				
      param.setParamTypCd(optLSCNames[i]);
      param.setParamVal(optLSC[i]);
      param.setModifiedBy("GESG_" + this.getClass().getSimpleName());
      param.setUpdateDate(LocalDateTime.now());				
      paramList.add(param);				
    }
  }
  return paramList;
}
```



## 3. getAfnsShockList()

[#1.3.-afnsshockgenerating](afnelsonsiegel.md#1.3.-afnsshockgenerating "mention")에서 산출한 시나리오 산출결과를 출력해서 충격시나리오결과 테이블(IR\_SPRD\_AFNS\_CALC) 에 적재.&#x20;

```java
public List<IrSprdAfnsCalc> getAfnsShockList() {		
  
  List<IrSprdAfnsCalc> shockList = new ArrayList<IrSprdAfnsCalc>();
      
if(this.IntShock != null) {			
    
for(int i=0; i<this.IntShock.numCols(); i++) {
   for(int j=0; j<this.IntShock.numRows(); j++) {
        
    IrSprdAfnsCalc shock = new IrSprdAfnsCalc();
    shock.setBaseYymm(dateToString(this.baseDate).substring(0,6));
    shock.setIrModelNm(this.mode);
    shock.setIrCurveNm(this.irCurveNm);				
    shock.setIrCurveSceNo(Integer.valueOf(this.IntShockName[i]));
    shock.setMatCd(
       String.format("%s%04d", 'M', (int) round(this.tenor[j] * MONTH_IN_YEAR, 0)));
    shock.setShkSprdCont(this.IntShock.get(j,i));
    shock.setModifiedBy("GESG_" + this.getClass().getSimpleName());
    shock.setUpdateDate(LocalDateTime.now());				
    shockList.add(shock);			
    }
  }
}
  return shockList;
}
```

