# Esg340\_BizScenHw1f.createScenHw1f





E\_IR\_DCNT\_RATE\_BU

```java
List<IrDcntSceStoBiz> stoBizList  = 
hw1f.getIrModelHw1fList().stream()
.map(s -> s.convert(applBizDv
                 , irModelId
                 , curveSwMap.getKey()
                 , swSce.getKey()
                 , jobId))
           .collect(Collectors.toList());
```

hw1f.getIrModelHw1fList()

