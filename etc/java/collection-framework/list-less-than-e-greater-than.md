---
description: List 컬렉션
---

# List\<E>

순서를 유지하고 저장 , 중복 허용.&#x20;

객체를 인덱스로 관리하기 때문에 객체를 저장하면 자동으로 인덱스가 부여됨. 해당 인덱스에 객체의 주소값을 참조하여 저장함. 추가, 검색, 삭제 메소드 제공.&#x20;

```java
// 인덱스 번호에 따라 처리 
List <Sting> list = ...
for (int i=0 ; i<list.size(); i++) {
String str = list.get(i) // i 인덱스에 저장된 Sting 객체를 가져옴 
}
```

```java
// 인덱스 번호가 필요없는 경우 
for (String str : list) { ...  }
```

## 1. ArrayList

List 인터페이스의 구현 클래스. ArrayList에 객체를 추가하면 객체가 인덱스로 관리됨.

배열(Array)이 크기가 고정되어 변경이 불가한 것에 비해 ArrayList는 갯수를 유동적으로 사용가능&#x20;

```java
List <String> List = new ArrayList<String>() ;
// 저장할 객체 타입을 타입 파라메터로 표기하고 기본 생성자 호출 
```

<details>

<summary>example</summary>

{% code title="private static void job150()" overflow="wrap" %}
```java
List<IrCurveSpot> rst = new ArrayList<IrCurveSpot>();
rst = Esg150_YtmToSpotSw.createIrCurveSpot
    (ytmRst.getKey()
    ,irCurveNm
    ,ytmRst.getValue()
    ,irCurveSwMap.get(irCurveNm).getSwAlphaYtm()
    ,irCurveSwMap.get(irCurveNm).getFreq()
    );
rst.forEach(s -> s.setIrCurve(irCurveMap.get(irCurveNm)));
if(rst.isEmpty()) throw new Exception();
rst.forEach(s -> session.save(s));
```
{% endcode %}

</details>

## 2. Vector

## 3. Stack

## 4. LinkedList
