# createParamHw1fNonSplitMap

```java
public static Map<String, List<?>> createParamHw1fNonSplitMap(
      String bssd
    , EIrModel irModelNm
    , List<IRateInput> spotList
    , List<IrVolSwpn> swpnVolList
    , double[] initParas
    , Integer freq
    , double errTol
    , int[] alphaPiece
    , int[] sigmaPiece) 
{
```

### input set&#x20;

```java
  Map<String, List<?>>  irParamHw1fMap  = new TreeMap<String, List<?>>();
  List<IrParamHwCalc>   paramCalc       = new ArrayList<IrParamHwCalc>();
  List<IrValidParamHw>  validParam      = new ArrayList<IrValidParamHw>();		
      
  freq = Math.max(freq, 1);		
  List<SwpnVolInfo> volInfo  = swpnVolList.stream().map(s-> SwpnVolInfo.convertFrom(s)).collect(toList());		
  
// add 23.04.18 		
  IrParamModel irModel  = IrParamModelDao.getParamModelList(irModelNm.getUpperIrModel()).get(0) ;
  IrCurve      irCurve  = irModel.getIrCurve();
  String     irCurveNm = irCurve.getIrCurveNm();
  
  Hw1fCalibrationKics calib = new Hw1fCalibrationKics
    (bssd, spotList, volInfo, alphaPiece, sigmaPiece, initParas, freq, errTol);
```

## Parameter 추정 (Main)

```java
paramCalc = calib.getHw1fCalibrationResultList()
         .stream().map(s -> s.convertNonSplit(irModelNm, irCurveNm))
         .flatMap(s-> s.stream())
         .collect(toList());
```

<details>

<summary>Main Function for Calibration Parameter</summary>

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
//	checkSwpnPriceHw();
//	checkSwpnVolDiff();

List<Hw1fCalibParas> hw1fParam = new ArrayList<Hw1fCalibParas>();
	
for(int i=0; i<this.sigmaPiece.length+1; i++) {			
  Hw1fCalibParas param = new Hw1fCalibParas();
  
  param.setBaseDate(dateToString(this.baseDate));
  int outerPiece = (int) this.lastLiquidPoint;
  
  if(i<this.sigmaPiece.length) {				
    param.setMonthSeq(this.sigmaPiece[i] * MONTH_IN_YEAR);
    param.setMatCd(String.format("%s%04d", TIME_UNIT_MONTH, this.sigmaPiece[i] * MONTH_IN_YEAR));
    param.setAlpha(Math.max(this.optParas[0], this.alphaMin));
    param.setSigma(Math.max(this.optParas[i+2], this.sigmaMin));
    param.setCost(this.costValue);
  }
  else {
    param.setMonthSeq(outerPiece * MONTH_IN_YEAR);
    param.setMatCd(String.format("%s%04d", TIME_UNIT_MONTH, outerPiece * MONTH_IN_YEAR));
    param.setAlpha(Math.max( (this.alphaPiece[0] < outerPiece ? this.optParas[1] : this.optParas[0]), this.alphaMin));
    param.setSigma(Math.max( (this.sigmaPiece[0] < outerPiece ? this.optParas[7] : this.optParas[2]), this.sigmaMin));
    param.setCost(this.costValue);
  }			
  hw1fParam.add(param);			
}
return hw1fParam;	
}
```

* swaption 가격 산출
  * [https://ojs.tripaledu.com/index.php/jefa/article/view/38/44](https://ojs.tripaledu.com/index.php/jefa/article/view/38/44)



</details>

### 그외 column set&#x20;

```java
  paramCalc.stream().forEach(s -> s.setIrParamModel(irModel));
  paramCalc.stream().forEach(s -> s.setIrCurve(irCurve));
  paramCalc.stream().forEach(s -> s.setModifiedBy(jobId));
  paramCalc.stream().forEach(s -> s.setUpdateDate(LocalDateTime.now()));
```

### Validation Result&#x20;

```java
validParam = calib.getValidationResult();
```

### 그외 column set&#x20;

```java
  validParam.stream().forEach(s -> s.setIrModelNm(irModelNm));
  validParam.stream().forEach(s -> s.setIrCurveNm(irCurveNm));
  validParam.stream().forEach(s -> s.setIrParamModel(irModel));
  validParam.stream().forEach(s -> s.setIrCurve(irCurve));
  validParam.stream().forEach(s -> s.setModifiedBy(jobId));
  validParam.stream().forEach(s -> s.setUpdateDate(LocalDateTime.now()));
  
  irParamHw1fMap.put("PARAM",  paramCalc);
  irParamHw1fMap.put("VALID",  validParam);
```

### log&#x20;

```java
log.info("{}({}) creates {} results of [MODEL: {}]. They are inserted into [{}] Table", jobId, EJob.valueOf(jobId).getJobName(), paramCalc.size(), irModelNm, toPhysicalName(IrParamHwCalc.class.getSimpleName()));
log.info("{}({}) creates {} results of [MODEL: {}]. They are inserted into [{}] Table", jobId, EJob.valueOf(jobId).getJobName(), validParam.size(), irModelNm, toPhysicalName(IrValidParamHw.class.getSimpleName()));

return irParamHw1fMap;
```
