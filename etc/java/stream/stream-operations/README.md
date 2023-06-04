---
description: 스트림에서 제공하는 연산 관련된 메서드
---

# Stream Operations

<figure><img src="../../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

|             intermediate operation ; 중간연산              |          terminal operation ; 최종연산          |
| :----------------------------------------------------: | :-----------------------------------------: |
|                     연결할 수 있는 스트림연산                     |                 스트림을 닫는 연산                  |
| 하나의 스트림을 다른 스트림으로 변환하는 연산, 스트림의 요소를 소비하지 않음. 파이프라인 구성  |          스트림의 요소를 소비하여 최종결과를 도출함.           |
|                  filter, sorted, map                   | count, findFirst, forEach, reduce, collect  |

<details>

<summary>(ref) interface Stream 스트림 메서드 목록  </summary>

*

    <figure><img src="../../../../.gitbook/assets/image (29) (2).png" alt=""><figcaption></figcaption></figure>

</details>



## 1. Intermediate operation (중간연산)&#x20;

<table><thead><tr><th width="149">Operation</th><th width="152">Return type</th><th>Functional IF</th><th>Fn discriptor</th></tr></thead><tbody><tr><td><a data-mention href="./#1.1.-filter">#1.1.-filter</a></td><td>Stream&#x3C;T></td><td>Predicate&#x3C;T></td><td>T -> boolean </td></tr><tr><td><a data-mention href="./#1.2.-map">#1.2.-map</a></td><td>Stream&#x3C;R></td><td>Function&#x3C;T,R></td><td>T -> R</td></tr><tr><td><a data-mention href="./#1.3.-flatmap">#1.3.-flatmap</a></td><td>Stream&#x3C;R></td><td>Function&#x3C;T, Stream&#x3C;R>></td><td>T->Stream&#x3C;R></td></tr><tr><td><a data-mention href="./#1.4.-sorted">#1.4.-sorted</a></td><td>Stream&#x3C;T></td><td>Comparator&#x3C;T></td><td>(T,T) -> int</td></tr><tr><td>distinct</td><td>Stream&#x3C;T></td><td></td><td></td></tr><tr><td>limt</td><td>Stream&#x3C;T></td><td>long</td><td></td></tr><tr><td>skip</td><td>Stream&#x3C;T></td><td>long</td><td></td></tr><tr><td>takeWhile</td><td>Stream&#x3C;T></td><td>Predicate&#x3C;T></td><td>T->boolean</td></tr><tr><td>dropWhile</td><td>Stream&#x3C;T></td><td>redicate&#x3C;T></td><td>T->boolean</td></tr></tbody></table>

### 1.1. filter()

```java
Stream<T> filter(Predicate<? super T> predicate)
```

파라미터로 받은 조건에 따라 긴지 아닌 판단 (`predicate`)하여 조건에 맞는 대상만 추출해서 새로운 스트림을 리리턴하는 메서드  Returns a stream consisting of the elements of this stream that match the given predicate.

* Parameters:`predicate` - a [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) predicate to apply to each element to determine if it should be included (각각의 요소를 포함해야할지 말지를 판단)



### 1.2. map()

```java
<R> Stream<R> map(Function<? super T,? extends R> mapper)
```

파라미터로 받은 fn을 이용해서 반환된 결과를 새로운 스트림으로 리턴하는 메서드. Returns a stream consisting of the results of applying the given function to the elements of this stream.

* Type Parameters:`R` - The element type of the new stream (리턴할 스트림의 타입을 의미함. 매핑결과가 타입이 달라질 수 있기 때문에 이렇게 선언함)&#x20;
* Parameters:`mapper` - a [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) function to apply to each element (각각의 요소에 대응되는 함수)



### 1.3. flatMap()

```java
<R> Stream<R> flatMap(Function<? super T,? extends Stream<? extends R>> mapper)
```

Returns a stream consisting of the results of replacing each element of this stream with the contents of a mapped stream produced by applying the provided mapping function to each element. Each mapped stream is [`closed`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/BaseStream.html#close--) after its contents have been placed into this stream. (If a mapped stream is `null` an empty stream is used, instead.)

* API Note:The `flatMap()` operation has the effect of applying a one-to-many transformation to the elements of the stream, and then flattening the resulting elements into a new stream.
* Type Parameters:`R` - The element type of the new stream
* Parameters:`mapper` - a [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) function to apply to each element which produces a stream of new values



### 1.4. sorted()

```java
Stream<T> sorted(Comparator<? super T> comparator)
```

Returns a stream consisting of the elements of this stream, sorted according to the provided `Comparator`. For ordered streams, the sort is stable. For unordered streams, no stability guarantees are made.

* Parameters:`comparator` - a [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) `Comparator` to be used to compare stream elements



## 2. Terminal operation (최종연산)&#x20;

<table><thead><tr><th width="151.66666666666666">Operation</th><th width="152">Return type</th><th>Functional IF</th><th>Fn discriptor</th></tr></thead><tbody><tr><td><a data-mention href="./#2.1.-foreach">#2.1.-foreach</a></td><td>void</td><td>Consumer&#x3C;T></td><td>T-> void</td></tr><tr><td><a data-mention href="./#2.2.-collect">#2.2.-collect</a></td><td>R</td><td>Collector&#x3C;T,A,R></td><td></td></tr><tr><td>reduce</td><td>Optional&#x3C;T></td><td>BinaryOperator&#x3C;T></td><td>(T,T) -> T</td></tr><tr><td>anyMatch</td><td>boolean</td><td>Predicate&#x3C;T></td><td>T -> boolean</td></tr><tr><td>noneMatch</td><td>boolean</td><td>Predicate&#x3C;T></td><td>T -> boolean</td></tr><tr><td>allMatch</td><td>boolean</td><td>Predicate&#x3C;T></td><td>T -> boolean</td></tr><tr><td>count</td><td>ong (generic)</td><td>long</td><td></td></tr><tr><td>findAny</td><td>Optional&#x3C;T></td><td></td><td></td></tr><tr><td>findFirst</td><td>Optional&#x3C;T></td><td></td><td></td></tr></tbody></table>

### 2.1. forEach()

```java
void forEach(Consumer<? super T> action)
```

Performs an action for each element of this stream.

The behavior of this operation is explicitly nondeterministic. For parallel stream pipelines, this operation does _not_ guarantee to respect the encounter order of the stream, as doing so would sacrifice the benefit of parallelism. For any given element, the action may be performed at whatever time and in whatever thread the library chooses. If the action accesses shared state, it is responsible for providing the required synchronization.

* Parameters:`action` - a [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference) action to perform on the elements



### 2.2. collect()

```java
<R> R collect(Supplier<R> supplier,
              BiConsumer<R,? super T> accumulator,
              BiConsumer<R,R> combiner)
```

스트림의 모든 요소를 수집하는 메서드 . `Collector` 인터페이스를 구현하는 객체를 인수로 받음.  Performs a [mutable reduction](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#MutableReduction) operation on the elements of this stream. (스트림의 요소들에 대해 가변적인(reduce) 연산을 수행함.)

<details>

<summary>Mutable reduction</summary>

결과 컨테이너(result container)가 가변적(mutable)인 연산을 의미.&#x20;

* 결과 컨테이너는, 예를 들어 `ArrayList`와 같은 자료구조를 의미함. 이러한 컨테이너는 요소가 추가됨에 따라 내부 상태가 변경됨.
* 즉, "mutable reduction"이란, 요소를 결과 컨테이너의 상태를 갱신하면서 추가하는 연산을 의미.

#### example

```java
javaCopy codeList<Integer> numb = Arrays.asList(1, 2, 3, 4, 5); 
List<Integer> result 
= numb.stream()
      .collect(ArrayList::new, ArrayList::add, ArrayList::addAll);
```

위 코드는 `numb` 리스트의 요소들을 가지고&#x20;

1. 새로운 `ArrayList` 객체를 생성하고,&#x20;
2. 각 요소를 해당 리스트에 추가하며,&#x20;
3. 최종적으로 결과 컨테이너에 모든 요소를 추가함.

* `ArrayList::new`는 초기값으로 빈 리스트를 생성하는 메서드
* `ArrayList::add`는 요소를 추가하는 메서드
* `ArrayList::addAll`은 여러 요소를 추가하는 메서드

즉, 위 코드는 `numb` 리스트의 요소들을 `ArrayList`에 추가하는 "mutable reduction" 연산을 수행하는것을 의미.

```java
// The following will take a stream of strings and concatenates them into a single string:
String concat 
= stringStream.collect(StringBuilder::new
                     , StringBuilder::append
                     , StringBuilder::append)
              .toString();
```

</details>

위 작업은 아래의 결과와 같음&#x20;

```java
R result = supplier.get();
 for (T element : this stream)
      accumulator.accept(result, element);
  return result;
```

* Type Parameters:`R` - type of the result
* Parameters:
  * `supplier` - a function that creates a new result container. For a parallel execution, this function may be called multiple times and must return a fresh value each time.
  * `accumulator` - an [associative](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Associativity), [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) function for incorporating an additional element into a result
  * `combiner` - an [associative](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Associativity), [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) function for combining two values, which must be compatible with the accumulator function
* Returns:the result of the reduction
