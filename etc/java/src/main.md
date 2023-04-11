# main

* 세션실행, 처리 건수 등을 위한 변수 정의&#x20;
* biz 실행 시 반복 처리를 위한 map (금리커브, biz구분 등 )&#x20;
* biz 로직에서 사용할 초기 입력변수 셋팅

## 1. code 실행&#x20;

<details>

<summary>static member </summary>

엔진에서 공통적으로 사용하는 필드, 메서드를 미리 정의함.&#x20;

{% code title="실행 환경설정 " %}
```java
Map<ERunArgument, String> argInputMap  = new LinkedHashMap<>();
Map<String, String>       argInDBMap   = new LinkedHashMap<>();	
List<String>              jobList      = new ArrayList<String>();

Session   session;
String    bssd;	

int       projectionYear              = 120;     // 프로젝션 기간                                        
long      cnt                         = 0;	 // 처리건수                 
int       flushSize                   = 10000;
int       logSize                     = 100000;	                                                     	
```
{% endcode %}

{% code title="처리 반복단위 " %}
```java
// 금리커브nm 기준으로 반복 
List<String>           irCurveNmList  = new ArrayList<String>();	
Map<String, IrCurve>   irCurveMap     = new TreeMap<String, IrCurve>();	
Map<String, IrParamSw> irCurveSwMap   = new TreeMap<String, IrParamSw>();
// biz 구분별 맵 : 금리커브|IrParamSw 단위로 반복함. 
Map<String, Map<Integer, IrParamSw>> kicsSwMap = new TreeMap<String, Map<Integer, IrParamSw>>();
Map<String, Map<Integer, IrParamSw>> ifrsSwMap = new TreeMap<String, Map<Integer, IrParamSw>>();
Map<String, Map<Integer, IrParamSw>> ibizSwMap = new TreeMap<String, Map<Integer, IrParamSw>>();
Map<String, Map<Integer, IrParamSw>> saasSwMap = new TreeMap<String, Map<Integer, IrParamSw>>();
```
{% endcode %}

{% code title="금리모형별 초기변수 설정 " %}
```java
double    hw1fInitAlpha               = 0.05;
double    hw1fInitSigma               = 0.007;		
double    targetDuration              = 3.0;
int[]     hwAlphaPieceSplit           = new int[] {10};
int[]     hwAlphaPieceNonSplit        = new int[] {20};
int[]     hwSigmaPiece                = new int[] {1, 2, 3, 5, 7, 10};
double    significanceLevel           = 0.05;	
int       cirAvgMonth                 = 36;	
int       cirPrjYear                  = 30;
String    iRateHisStBaseDate          = "20100101";	
```
{% endcode %}

</details>

## 2. init(String\[] args)

<details>

<summary>properties</summary>

**argInputMap 생성**&#x20;

실행시 입력변수 arg 를 입력받아 **ERunArgument** 속성에 따라 분류&#x20;

* **arg**&#x20;
  * \-Dtime=2022-12-31, -Dproperties=/Users/sunyoung/git/esgTest/NESG/gesg.properties
* **argInputMap** LinkedHashMap\<K,V>
  * {time=2022-12-31, properties=/Users/sunyoung/git/esgTest/NESG/gesg.properties}

<!---->

* **ERunArgument**&#x20;
  * time TIME
  * job JOB
  * properties PROPERTIES
  * encrypt ENCRYPT

#### properties 정보 생성&#x20;

```java
Properties properties = new Properties();
try {
  FileInputStream fis = new FileInputStream(argInputMap.get(ERunArgument.properties));
  properties.load(new BufferedInputStream(fis));			
  EsgConstant.TABLE_SCHEMA = properties.getOrDefault("schema", "PUBLIC").toString().trim().toUpperCase();
  
  if(properties.containsKey("encrypt") && properties.getProperty("encrypt").toString().trim().toUpperCase().equals("Y")) {
    // db 계정 비밀번호 암호화 처리 
    AesCrypto aes128 = new AesCrypto();
    String decodePwd = aes128.AesCBCDecode(properties.getProperty("password"));
    properties.setProperty("password", decodePwd);
  }
  
} catch (Exception e) {
  log.error("Error in Properties Loading : {}", e);
  System.exit(0);
}
```



</details>

<details>

<summary>session 생성 </summary>

```java
session = HibernateUtil.getSessionFactory().openSession();
log.info("End of session call");	
```

[#vs](../../db/#vs "mention")

* 데이터베이스에서 세션은, 데이터베이스 접속을 시작으로 여러 데이터베이스에서 관련 작업을 수행한 후 접속을 종료하기까지 전체 기간을 의미한다.
  * 세션은 main에서 한번만 열고 트랜젝션은 job작업마다 call하고 있음.&#x20;
* 트랜잭션은 데이터 조작 명령어가 모인 하나의 작업 단위를 뜻하고, 세션 내부에는 하나 이상의 트랜잭션이 존재한다. 즉 세션이 트랜잭션보다 큰 범위의 개념이다

</details>

<details>

<summary>properties (DB :  default) </summary>

argInDBMap (table에 설정된 properties 설정정보 읽어와서 처리)&#x20;

* 엔진에서 산출에 필요한 상수를 db에서 읽어옴&#x20;
* static 변수랑 겹침 !! 아래에서 default 처리함&#x20;
* CO\_ESG\_META / GROUP\_ID ='PROPERTIES'

```java
argInDBMap = CoEsgMetaDao.getCoEsgMeta("PROPERTIES").stream()
        .collect(toMap(s->s.getParamKey(), s->s.getParamValue()));	
log.info("argInDBMap: {}", argInDBMap);
```

```java
argInDBMap: {
  AFNS_CONF_INTERVAL=0.995
, SIGNIFICANCE_LEVEL=0.05
, BOND_YIELD_TGT_DURATION=3
, IR_HIS_START_DATE=20100101
, HW_SIGMA_AVG_NUM=120
, HW_ALPHA_AVG_NUM=120
, CIR_PROJECTION_YEAR=30
, HW1F_SIGMA_INIT=0.007
, LP_CURVE_ID=5010110
, CIR_AVG_MONTH=36
, HW1F_ALPHA_INIT=0.05
, PROJECTION_YEAR=120
, AFNS_WEEK_DAY=5
}
```

</details>

<details>

<summary>jobList</summary>

* 실행할 작업 목록 불러오기&#x20;
* CO\_JOB\_LIST /  USE\_YN ='Y

```java
CoJobListDao.getCoJobList().stream()
    .forEach(s -> log.info("JOB LIST: {}, {}"
    , s.getJobNm().trim()
    , s.getJobName().trim()));
    
jobList    = CoJobListDao.getCoJobList().stream()
    .map(s -> s.getJobNm().trim()).collect(Collectors.toList());
```

</details>

<details>

<summary>금리 모형별 입력변수 설정 </summary>

* CO\_ESG\_META에서 정의한 설정을  static field에 반영.&#x20;
* 설정이 누락된 경우 default 처리

```java
hw1fInitAlpha                = Double.parseDouble(argInDBMap.getOrDefault("HW1F_ALPHA_INIT", "0.05" ).toString());
hw1fInitSigma                = Double.parseDouble(argInDBMap.getOrDefault("HW1F_SIGMA_INIT", "0.007").toString());			

String hwAlphaPieceStr       = argInDBMap.getOrDefault("HW1F_ALPHA_PIECE", "10").toString();
String hwSigmaPieceStr       = argInDBMap.getOrDefault("HW1F_SIGMA_PIECE", "1, 2, 3, 5, 7, 10").toString();
hwAlphaPieceSplit            = Arrays.stream(hwAlphaPieceStr.split(",")).map(s -> s.trim()).map(Integer::parseInt).mapToInt(Integer::intValue).toArray();
hwSigmaPiece                 = Arrays.stream(hwSigmaPieceStr.split(",")).map(s -> s.trim()).map(Integer::parseInt).mapToInt(Integer::intValue).toArray();				

iRateHisStBaseDate           = argInDBMap.getOrDefault("IR_HIS_START_DATE", "20100101").toString().trim().toUpperCase();
projectionYear 	             = Integer.parseInt(argInDBMap.getOrDefault("PROJECTION_YEAR", "120").toString());

targetDuration	             = Double.parseDouble(argInDBMap.getOrDefault("BOND_YIELD_TGT_DURATION", "3.0").toString());		
significanceLevel            = Double.parseDouble(argInDBMap.getOrDefault("SIGNIFICANCE_LEVEL", "0.05").toString());

cirAvgMonth                  = Integer.parseInt(argInDBMap.getOrDefault("CIR_AVG_MONTH", "36").toString());
cirPrjYear                   = Integer.parseInt(argInDBMap.getOrDefault("CIR_PROJECTION_YEAR", "30").toString());
```

</details>

## 3. main job (job110 ... job820)&#x20;

|              |                                  |                                                         |
| :----------: | :------------------------------: | ------------------------------------------------------- |
|   prepare    | [job110.md](job110.md "mention") | Set Smith-Wilson Attribute                              |
|    prepare   | [job120.md](job120.md "mention") | Set Swaption Volatility                                 |
|  기본 무위험 금리커브 | [job130.md](job130.md "mention") | Set YTM TermStructure                                   |
|  기본 무위험 금리커브 | [job150.md](job150.md "mention") | YTM to SPOT by Smith-Wilson Method                      |
| 조정 무위험 금리커브  |    [job210](job210/ "mention")   | AFNS Weekly Input TermStructure Setup                   |
|  조정 무위험 금리커브 | [job220.md](job220.md "mention") | AFNS Shock Spread                                       |
|  조정 무위험 금리커브 | [job230.md](job230.md "mention") | Biz Applied AFNS Shock Spread                           |
|  조정 무위험 금리커브 | [job240.md](job240.md "mention") | Set Liquidity Premium                                   |
|  조정 무위험 금리커브 | [job240.md](job240.md "mention") | Biz Applied Liquidity Premium                           |
|  조정 무위험 금리커브 |    [job260](job260/ "mention")   | BottomUp Risk Free TermStructure with Liquidity Premium |
|  조정 무위험 금리커브 |    [job270](job270/ "mention")   | Interpolated TermStructure by SW                        |
|  조정 무위험 금리커브 | [job280.md](job280.md "mention") | Biz Applied TermStructure by SW                         |
|              |                                  |                                                         |

#### TODO : 반복되는 처리구간 중복되는 로직 없는지 확인하기&#x20;

* JOB240\~JOB260 : (loop) BIZ -> IRCURVE -> SCENARIO -> TENOR&#x20;
* JOB260 : (loop) BIZ -> IRCURVE&#x20;
* JOB270 :  (loop) BIZ -> IRCURVE -> SCENARIO



## 4. main 종료&#x20;

{% code title="세션종료 " %}
```java
HibernateUtil.shutdown();
System.exit(0);
```
{% endcode %}
