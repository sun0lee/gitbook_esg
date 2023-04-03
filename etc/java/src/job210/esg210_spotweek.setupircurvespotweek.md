# Esg210\_SpotWeek.setupIrCurveSpotWeek()

## 1. 금리내역 가져오기&#x20;

IrCurveSpot에서 baseTenor 단위로 금리내역 정보를 가져온다. 이때 없거나 충분하지 않은 경우 종료.&#x20;

{% code title="curveHisList" %}
```java
List<IrCurveSpotWeek> rstList  = new ArrayList<IrCurveSpotWeek>();
List<IrCurveSpot> curveHisList = IrCurveSpotDao.getIrCurveSpotListHis
	( bssd
	, stBssd
	, irCurveId
	, tenorList
	);
// 없거나 충분하지 않은 경우 비어있는 rstList를 리턴함. 
if(curveHisList.size()==0) { 
	log.warn("IR Curve History of {} Data is not found at from {} to {}"
	, irCurveId
	, stBssd
	, bssd);
	return rstList;
}	
if(curveHisList.size() < 1000) { 
	log.warn(
   "Weekly SpotRate Data is not Enough [ID: {}, SIZE: {}] from {} to {}"
	, irCurveId
	, curveHisList.size()
	, stBssd
	, FinUtils.toEndOfMonth(bssd));			
	return rstList;
}
```
{% endcode %}

<details>

<summary>getIrCurveSpotListHis()</summary>

```java
public static List<IrCurveSpot> getIrCurveSpotListHis
( String bssd
, String stBssd
, String irCurveNm
, List<String> tenorList
) {

String query = " select a from IrCurveSpot a    " 
             + "  where a.irCurveNm =:irCurveNm "			
             + "    and a.baseDate >= :stBssd   "
             + "    and a.baseDate <= :bssd     "
             + "    and a.matCd in (:matCdList) "
             + "  order by a.baseDate, a.matCd  "
;

return session.createQuery(query, IrCurveSpot.class)
             .setParameter("irCurveNm", irCurveNm)
             .setParameter("stBssd", stBssd)
             .setParameter("bssd", FinUtils.toEndOfMonth(bssd))
             .setParameterList("matCdList", tenorList)
             .getResultList()
             ;
}
```

</details>

## 2.  영업일,휴일 구분

* curveHisList에서 날짜를 추출해서 비어있는 날짜 구분 (emptySet)
* 특정 요일로 금리 내역을 조회해서 영업일이 아닌 경우 예외처리를 위해 구분함. &#x20;

```java
TreeSet<LocalDate> dateSet  =  new TreeSet<LocalDate>
	(curveHisList.stream()
	.map(s-> LocalDate.parse(s.getBaseDate(), DateTimeFormatter.BASIC_ISO_DATE))
	.collect(Collectors.toSet()));
	
TreeSet<LocalDate> emptySet =  new TreeSet<LocalDate>();
	
LocalDate sttDate = dateSet.first();
LocalDate endDate = DateUtil.stringToDate(DateUtil.toEndOfMonth(bssd));
int dayDiff       = (int) ChronoUnit.DAYS.between(sttDate, endDate);		

for(int i=0; i<=dayDiff; i++) {			
	LocalDate curDate = sttDate.plusDays(i);
	if(!dateSet.contains(curDate)) {
		dateSet.add(curDate);
		emptySet.add(curDate);
	}
}
```

## 3. 영업일 데이터 적재&#x20;

IrCurveSpot에 존재하는 데이터는 영업일 데이터 이므로 영업일 Y로 적재됨.&#x20;

{% code title="" %}
```java
rstList = curveHisList.stream()
    .map(s-> s.convertToWeek())
    .collect(Collectors.toList());

TreeMap<String, List<IrCurveSpotWeek>> weekHisMap
 = new TreeMap<String, List<IrCurveSpotWeek>>();		
weekHisMap = rstList.stream().collect
(Collectors.groupingBy(s -> s.getBaseDate(), TreeMap::new, Collectors.toList()));
```
{% endcode %}

## 4. 휴일 데이터 적재&#x20;

휴일인 경우 spot rate정보가 없으므로 직전 영업일 기준 정보를 영업일구분 N로 적재함. &#x20;

```java
List<IrCurveSpotWeek> curData = weekHisMap.firstEntry().getValue();

for(LocalDate date : dateSet) {			
 String dateStr = DateUtil.dateToString(date);
 if(weekHisMap.containsKey(dateStr)) {		
	curData = weekHisMap.get(dateStr);			
    }
 else {
	List<IrCurveSpotWeek> cloneData = new ArrayList<IrCurveSpotWeek>();
// 휴일인 경우 직전 영업일정보를 복제 
  for(IrCurveSpotWeek data : curData) cloneData.add(new IrCurveSpotWeek(data));	
    cloneData.forEach(s -> s.setBaseDate(dateStr)); //날짜 변환 
    cloneData.forEach(s -> s.setDayOfWeek(date.getDayOfWeek().name()));
    cloneData.forEach(s -> s.setBizDayType("N"));
    weekHisMap.put(dateStr, cloneData);				
  }
}
```

## 4. 결과 return&#x20;

```java
log.info("{}({}) creates {} results. They are inserted into [{}] Table"
    , jobId
    , EJob.valueOf(jobId).getJobName()
    , weekHisMap.size() * weekHisMap.firstEntry().getValue().size()
    , toPhysicalName(IrCurveSpotWeek.class.getSimpleName())
    );

return weekHisMap.values().stream()
                .flatMap(s -> s.stream()).collect(Collectors.toList());		
```

#### Job210 [#4.-spotweek](./#4.-spotweek "mention")
