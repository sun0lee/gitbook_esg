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
  
  Hw1fCalibrationKics calib = new Hw1fCalibrationKics(bssd, spotList, volInfo, alphaPiece, sigmaPiece, initParas, freq, errTol);
//		Hw1fCalibrationKicsNs calib = new Hw1fCalibrationKicsNs(bssd, spotList, volInfo, alphaPiece, sigmaPiece, initParas, freq, errTol);
```



##

```java
paramCalc = calib.getHw1fCalibrationResultList().stream().map(s -> s.convertNonSplit(irModelNm, irCurveNm))
         .flatMap(s-> s.stream())
         .collect(toList());
```





```java
  paramCalc.stream().forEach(s -> s.setIrParamModel(irModel));
  paramCalc.stream().forEach(s -> s.setIrCurve(irCurve));
  paramCalc.stream().forEach(s -> s.setModifiedBy(jobId));
  paramCalc.stream().forEach(s -> s.setUpdateDate(LocalDateTime.now()));

```

```java
```



##

```java
```

##

```java
```

