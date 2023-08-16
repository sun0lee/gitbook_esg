---
description: HW1F 모형기반 시나리오를 생성하고, 그때 사용된 난수를 적재함. 마팅게일 테스트(시나리오의 적정성 검증)를 위한 검증 결과를 적재함.
---

# generate Scenario (340)

## Check

*

## Table&#x20;

<table data-view="cards"><thead><tr><th></th><th>entity</th><th>table</th></tr></thead><tbody><tr><td>input</td><td></td><td></td></tr><tr><td>output</td><td><p>IrParamHwRnd</p><p>IrDcntSceStoBiz</p><p>IrValidSceSto</p></td><td><p>IR_PARAM_HW_RND</p><p>IR_DCNT_SCE_STO_BIZ</p><p>IR_VALID_SCE_STO</p></td></tr></tbody></table>



## Work Detail

### 1. 시나리오 생성;  IR\_DCNT\_SCE\_STO\_BIZ

```java
hw1fResult = Esg340_BizScenHw1f.createScenHw1f
( bssd
, biz.getKey()
, irModelId
, curveSwMap.getKey()
, swSce.getKey()
, biz.getValue()
, modelMstMap
, projectionYear)
;
```

```java
List<IrDcntSceStoBiz> stoSceList = (List<IrDcntSceStoBiz>) hw1fResult.get("SCE");
```

### 2. 난수적재 ; IR\_PARAM\_HW\_RND

```java
List<IrParamHwRnd>    randHwList = (List<IrParamHwRnd>) hw1fResult.get("RND");
```

### ~~3. 할인율 적정성 검증 ; IR\_VALID\_SCE\_STO~~ -> job 370에서 처리함.&#x20;
