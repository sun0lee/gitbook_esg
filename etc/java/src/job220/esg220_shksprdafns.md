# Esg220\_ShkSprdAfns()

<details>

<summary>내부 변수 (input)</summary>

`curveBaseList` : 평가시점 금리커브를 기준으로 fitting 시킴. &#x20;

`curveHisList` 과거 금리 내역 :&#x20;

* &#x20;\= weekHisList.stream().map(s->s.convertToHis()).collect(toList());
* weekHisBizList :  매주 금요일 데이터만 추출 (영업일 기준)  => 사용안함 !
* weekHisList :  매주 금요일 데이터만 추출 (영업일 구분 없이)  => 적용&#x20;

IR\_PARAM\_MODEL.ITR\_TOL : 수렴오차 0.00000001

</details>

### Esg220\_ShkSprdAfns

<details>

<summary>AFNelsonSiegel afns = new AFNelsonSiegel( ... )</summary>

* 생성자 , input 세팅&#x20;
*

    <figure><img src="../../../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

</details>

<details>

<summary>irScenarioList.addAll(afns.getAfnsResultList());</summary>

#### `initializeAfnsParas();`&#x20;

파라메타 초기화&#x20;

#### `if(this.inputParas != null) this.initParas = this.inputParas;`

모수 뭐 쓸지 결정. this.inputParas가 null이 아니면 초기모수값 설정.&#x20;

#### `kalmanFiltering();`

초기모수를 기준으로 뭔가를 최소화 하는 과정으로 모수를 추정하는 듯.  최적화된 모수를 설정함. (return 없이 모수세트 값만 지정하고 끝)

#### `afnsShockGenerating();`

충격수준 적용

* BaseShock
* MeanRShock
* LevelShock
* TwistShock

</details>

<details>

<summary>irShockParam. addAll(afns.getAfnsParamList());</summary>

파라메타 값 저장&#x20;

"LAMBDA" , "THETA\_1" , "THETA\_2" , "THETA\_3" , "KAPPA\_1" , "KAPPA\_2" , "KAPPA\_3", "SIGMA\_11", "SIGMA\_21", "SIGMA\_22", "SIGMA\_31", "SIGMA\_32", "SIGMA\_33", "EPSILON"

</details>

<details>

<summary>irShock. addAll(afns.getAfnsShockList());</summary>

충격수준 산출결과 저장&#x20;

</details>

