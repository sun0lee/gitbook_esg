# Esg220\_ShkSprdAfns

* 생성자 만들기 (필요한 입력변수 설정)&#x20;
* 실질적인 금리모형 정의 및 시나리오 산출 작업은 AFNelsonSiegel.class에서 정의함.&#x20;
* Esg220\_ShkSprdAfns.class에서는 입력변수 정의 및 할 일 나열 그리고 금리모형결과 외 부수적인 설정. &#x20;

```java
Map<String, List<?>> irShockSenario = new TreeMap<String, List<?>>();
irShockSenario = Esg220_ShkSprdAfns.createAfnsShockScenario(FinUtils.toEndOfMonth(bssd)
  , curveHisList
  , curveBaseList
  , irModelMst  // add 
  , irparamSw   // add 
  , argInDBMap  // add 
  );	
```

[afnelsonsiegel.md](../../../../biz-logic/interest-rate-model/afns/afnelsonsiegel.md "mention")

```java
// 1. 모수추정, 시나리오 생성 
irScenarioList.addAll(afns.getAfnsResultList());
// 2. 모수추정 결과 
irShockParam.addAll(afns.getAfnsParamList());
// 3. 충격시나리오 결과 
irShock.addAll(());

// fk 값 추가 
irShockParam.stream().forEach(s -> s.setIrParamModel(irModelMst));
irShock.     stream().forEach(s -> s.setIrParamModel(irModelMst));
irShockParam.stream().forEach(s -> s.setIrCurve(irModelMst.getIrCurve()));
irShock.     stream().forEach(s -> s.setIrCurve(irModelMst.getIrCurve()));
irShockParam.stream().forEach(s -> s.setModifiedBy(jobId));
irShock.     stream().forEach(s -> s.setModifiedBy(jobId));

irShockSenario.put("PARAM",  irShockParam);
irShockSenario.put("SHOCK",  irShock);
```
