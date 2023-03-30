---
description: java.util.stream.Collectors / 스트림의 최종연산에 사용되는 Collectors ??
---

# Collector\<T,A,R>

the argument passed to the `collect()` method is an implementation of the `Collector` interface, which  is a recipe for how to build a summary of the elements in the Stream.

`collect()` 메서드에 `Collector` 인터페이스(functional Interface)의 구현\__스트림의 요소를 어떤식으로 도출할지 지정하는\__ 을 전달함.&#x20;

{% code overflow="wrap" %}
```java
Map<Curr,List<Bid>> bidByCurr 
= bid.stream().collect(groupingBy)(Bid::getCurr));
// 범용적인 컬렉터 파라미터(;groupingBy)를 collect 메서드에 전달.  
```
{% endcode %}

```java
List<String> dishes 
= menu.stream()
    .map(Dish::getName)
    .collect(toList()); 
// toList를 Collector의 인터페이스의 구현으로 사용함. 
```

<details>

<summary>public interface Collector&#x3C;T, A, R></summary>

* T, The **type of input** elements to the reduction operation.
* A, The mutable **accumulation type** of the reduction operation.
* R, The **result type** of the reduction operation.

```java
public interface Collector<T, A, R> {

    Supplier<A> supplier();
    BiConsumer<A, T> accumulator();
    BinaryOperator<A> combiner();
    Function<A, R> finisher();
    Set<Characteristics> characteristics();
    
    public static<T, R> Collector<T, R, R> of(Supplier<R> supplier,
                                              BiConsumer<R, T> accumulator,
                                              BinaryOperator<R> combiner,
                                              Characteristics... characteristics) {
        Objects.requireNonNull(supplier);
        Objects.requireNonNull(accumulator);
        Objects.requireNonNull(combiner);
        Objects.requireNonNull(characteristics);
        Set<Characteristics> cs = (characteristics.length == 0)
                                  ? Collectors.CH_ID
                                  : Collections.unmodifiableSet(EnumSet.of(Collector.Characteristics.IDENTITY_FINISH,
                                                                           characteristics));
        return new Collectors.CollectorImpl<>(supplier, accumulator, combiner, cs);
    }

    public static<T, A, R> Collector<T, A, R> of(Supplier<A> supplier,
                                                 BiConsumer<A, T> accumulator,
                                                 BinaryOperator<A> combiner,
                                                 Function<A, R> finisher,
                                                 Characteristics... characteristics) {
        Objects.requireNonNull(supplier);
        Objects.requireNonNull(accumulator);
        Objects.requireNonNull(combiner);
        Objects.requireNonNull(finisher);
        Objects.requireNonNull(characteristics);
        Set<Characteristics> cs = Collectors.CH_NOID;
        if (characteristics.length > 0) {
            cs = EnumSet.noneOf(Characteristics.class);
            Collections.addAll(cs, characteristics);
            cs = Collections.unmodifiableSet(cs);
        }
        return new Collectors.CollectorImpl<>(supplier, accumulator, combiner, finisher, cs);
    }
    enum Characteristics {
        CONCURRENT,
        UNORDERED,
        IDENTITY_FINISH
    }
}
```



</details>

## 1. Stream.collect()

데이터의 중간처리 후 마지막에 원하는 형태로 변환해주는 역할을 하는 메서드.&#x20;

Stream.class는 stream이 해야할 일들을 정의한 interface이고, 여기에 정의된 collect()는 추상메서드임. 여기에서 직접 구현하지 않음 (하기로 한 일들에 대한 명세만 있을 뿐임 )

아래는 Stream.collect()가 해주기로 한 일의 목록.&#x20;

* stream 요소들을 List, Set, Map 등 Collection 자료형으로 변환
* stream 요소들의 결합 (joining)
* stream 요소들의 통계 (최대, 최소, 갯수, 평균 등 ) &#x20;
* stream 요소들의 grouping / partitioning

## 2. Collectors.class&#x20;

그럼 Stream.class (인터페이스)에서 정의한 일들을 실제로 누가 구현하냐면 Collectors.class가 함. 그래서 미리 정의된 모든 구현은 Collectors 클래스에 찾을 수 있음. 약속된 기능을 사용해야 할때는 실제로 구현한 Collectors를 import하고. call 할 때에는 인터페이스의 약속된 형태로 calll 하는 것임.&#x20;

### 2.1. Collectors.toList()

모든 stream의 요소를 List 인스턴스로 수집하는데 사용할 수 있음. 특정한 List를 구현하는 것이 아님 (??)

{% code overflow="wrap" %}
```java
import static java.util.stream.Collectors.toList ;

List<String> rst = sampleList.stream().collect(toList());
```
{% endcode %}

### 2.2. Collectors.toSet()&#x20;

모든 Stream의 요소를 Set 인스턴스로 수집하는데 사용할 수 있음.&#x20;

```java
import static java.util.stream.Collectors.toSet ;

Set<String> rst = 
    sampleSet.stream().collect(toSet());
```

### 2.3. Collectors.toCollection()

toList, toSet Collector는 특정한 List, Set의 구현을 지정할 수 없음. 특정 Collection을 구현하려면 toCollectionCollector를 이용해야 함. (??)

{% embed url="https://www.geeksforgeeks.org/java-stream-collectors-tocollection-in-java/" %}

### 2.4. Collectors.toMap()

```java
import static java.util.stream.Collectors.toMap ;
Map<String, Integer> rst = 
    sampleMap.stream().collect(toMap(Function.identity(),String::length))
    
    //key  Function.identity()
    //value  String::length
```



{% embed url="https://www.daddyprogrammer.org/post/1163/java-collectors/" %}
