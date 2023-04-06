---
description: stream().collect(Collectors.toList())
---

# stream().collect()

자주 사용되는 최종 연산중에 하나인 collect() 의 구체적인 예시를 통해 어떻게 결과 값을 반환하는지 확인함.&#x20;

{% code overflow="wrap" %}
```java
import java.util.stream.Collectors ; 
//Collectors구현 클래스를 임포트 하는경우  

List<String> rst = sampleList.stream().collect(Collectors.toList()); 
// collect()의 인자는 객체.매서드()
```
{% endcode %}



{% code overflow="wrap" %}
```java
import static java.util.stream.Collectors.toList ; 
// 정적 메서드 자체를 임포트 하는 경우 

List<String> rst = sampleList.stream().collect(toList()); 
//collect()의 인자는 toList 메서드만 call을 해도 됨.(정적메서드라 객체 없이 써도 됨)
```
{% endcode %}



## 0. 개념&#x20;

|                               |           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ----------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| collect()                     | mathod    | <p><code>collect()</code> 메서드는 <code>Collector</code> 인터페이스를 구현하는 객체를 인수로 받음. </p><p>이 객체는 스트림에서 요소를 수집하는 데 사용되는 로직을 구현함.</p><p> 즉, <code>Collector</code> 객체는 스트림의 요소를 수집하여 최종 결과 객체를 생성함.</p><p><code>List aa  = strings.stream() .collect(Collectors.toList())</code></p><ul><li><code>Collectors.toList()</code>; collector인터페이스 구현한 객체 </li></ul>                                                                                                                                           |
| `Collector`                   | interface | <p>스트림 요소들을 수집하는 데 사용되는 연산자 인터페이스.</p><p>이 인터페이스는 다음과 같은 세 가지 메서드를 선언함. </p><p></p><p>public interface Collector&#x3C;T, A, R> { </p><p>Supplier supplier(); </p><p>BiConsumer&#x3C;A, T> accumulator(); </p><p>Function&#x3C;A, R> finisher(); </p><p>}</p><ul><li> <code>T</code>는 수집할 요소의 타입, <code>A</code>는 수집 중간 결과 객체의 타입, <code>R</code>은 최종 결과 객체의 타입</li><li>즉, <code>Collector&#x3C;T,A,R></code>은 T 타입의 요소들을 A 타입의 중간 결과 객체에 수집하고, 최종적으로 R 타입의 결과 객체를 반환하는 컬렉터를 표현.</li></ul><p></p> |
| `java.util.stream.Collectors` | class     | <ul><li>Java 8에서 추가된 스트림 API의 일부.  </li><li><code>Collector</code> 인터페이스를 구현하는 여러 가지 유용한 컬렉터를 제공하는 유틸리티 클래스 </li><li>아래의 메서드들을 제공함 </li><li><code>Collectors.toSet()</code>, <code>Collectors.joining()</code>, <code>Collectors.toMap()</code>, <code>Collectors.groupingBy()</code></li></ul>                                                                                                                                                                                                  |



## 1. Stream.collect()

#### 데이터의 중간처리 후 마지막에 원하는 형태로 변환해주는 역할을 하는 "추상" 메서드.&#x20;

Stream.class는 stream이 해야할 일들을 정의한 interface이고, 여기에 정의된 collect()는 추상메서드임. 여기에서 직접 구현하지 않음 (하기로 한 일들에 대한 명세만 있을 뿐임 )

아래는 Stream.collect()가 해주기로 한 일의 목록.&#x20;

* stream 요소들을 List, Set, Map 등 Collection 자료형으로 변환
* stream 요소들의 결합 (joining)
* stream 요소들의 통계 (최대, 최소, 갯수, 평균 등 ) &#x20;
* stream 요소들의 grouping / partitioning



## 2. Collector\<T, A, R>

#### public interface Collector\<T, A, R>

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
}
```



## 3. Collectors.class&#x20;

Stream.class (인터페이스)에서 정의한 일들을 실제로 누가 구현하냐면 Collectors.class가 함. 미리 정의된 모든 구현은 Collectors 클래스에 찾을 수 있음. 약속된 기능을 사용해야 할때는 실제로 구현한 Collectors를 import하고. call 할 때에는 인터페이스의 약속된 형태로 calll 하는 것임.&#x20;

#### 2.1. Collectors.toList()

#### 2.2. Collectors.toSet()&#x20;

#### 2.3. Collectors.toMap()



