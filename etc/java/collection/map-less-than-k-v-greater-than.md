---
description: Map 컬렉션
---

# Map\<K,V>

Map 컬렉션은 키Key와 값Value으로 구성된 Enrty 객체를 저장하는 구조. key는 중복 비허용&#x20;

```java
Map <String, Integer> map = ... ;
map.put("age,22") ; 
```

* 메서드&#x20;
  * `get()` ; Map 내에 key 값 알아내기
  * Map 내에 value 값 알아내기
    1. `entrySet`
    2. `KeySet`
    3. `forEach` (lambda)

{% code overflow="wrap" %}
```java
// 1. entrySet
TreeMap<String, List<IrCurveYtm>> ytmRstMap = new TreeMap<String, List<IrCurveYtm>>();
ytmRstMap = ytmRstList.stream().collect(Collectors.groupingBy(s -> s.getBaseDate(), TreeMap::new, Collectors.toList()));					

// entrySet example 
//treeMap에 value 목록들을 뽑아내서 반복해줘.  
for(Map.Entry<String, List<IrCurveYtm>> ytmRst : ytmRstMap.entrySet()) {
  ... ;
}
```
{% endcode %}

```java
// 2. KeySet
for (string Key : map.KeySet()){
  ... ;
}
```

```java
// 3. forEach
map.forEach((key,value) ->  ... ; )
```

<details>

<summary>forEach() 메서드 참조 </summary>

`forEach()` 메서드는 함수적 인터페이스 타입([functional-interface](../behavior-parameterization/functional-interface/ "mention"))을 매개값으로 가지므로 컬렉션의 요소를 소비할 코드를 람다식으로 기술.&#x20;

(스트림이 제공하는 요소처리 메서드는 함수형 인터페이스 타입( [consumer-less-than-t-greater-than.md](../behavior-parameterization/functional-interface/consumer-less-than-t-greater-than.md "mention"))이므로 람다식([lambdas.md](../behavior-parameterization/lambdas.md "mention")), 메서드참조 ([method-references.md](../behavior-parameterization/method-references.md "mention"))를 이용하여 요소처리 내용을 인자로 전달할 수 있음.)

```java
void forEach(Consumer<T> action)
```

</details>



## Map 구현 클래스&#x20;

### 1. HashMap&#x20;

HashMap은 Map 인터페이스를 구현한 대표적인 map 컬렉션. key와 value로 null을 허용함. HashMap의 키로 사용할 객체는 hashCode() 메서드의 리턴값이 같고 equals() 메서드가 true를 리턴할 경우 동일 키로 판단하여 중복 저장하지 않음. [#hash](set-less-than-e-greater-than.md#hash "mention")

```java
Map<K,V> map = new HashMap<K,V> ();
Map <String, Integer> map = new HashMap <String, Integer>(); 
```



### 2. HashTable

HashMap과 동일한 구조를 가지고 있으나. 단 HashTable은 동기화된 synchronized 메서드로 구성되어 있어서 멀티 스레드가 동시에 HashTable의 메서드를 실행시킬수 없는 차이점이 있음. &#x20;

### SortedMap

### 3.TreeMap

binary tree를 기반으로 한 Map 컬렉션. TreeSet과 차이점은 키와 값이 저장된 Entry를 저장한다는 점임. 내부의 값(엔트리)들을 key값을 기준으로 <mark style="color:blue;">정렬</mark>하여 가지고 있음.(Red-Black Tree 자료구조)

* key기준 순서를 가지고 있음 !!!&#x20;

{% embed url="https://sabarada.tistory.com/139" %}



### 4. EnumMap

key를 Enum 타입으로 한정하는 Map&#x20;

### 5. Properties &#x20;
