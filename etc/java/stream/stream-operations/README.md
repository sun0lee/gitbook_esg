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

## 1. Intermediate operation (중간연산)&#x20;

| Operation (method                          | Return type | Functional IF            | Fn discriptor |
| ------------------------------------------ | ----------- | ------------------------ | ------------- |
| [#1.1.-filter](./#1.1.-filter "mention")   | Stream\<T>  | Predicate\<T>            | T -> boolean  |
| [#1.2.-map](./#1.2.-map "mention")         | Stream\<R>  | Function\<T,R>           | T -> R        |
| [#1.3.-flatmap](./#1.3.-flatmap "mention") | Stream\<R>  | Function\<T, Stream\<R>> | T->Stream\<R> |
| [#1.4.-sorted](./#1.4.-sorted "mention")   | Stream\<T>  | Comparator\<T>           | (T,T) -> int  |
| distinct                                   | Stream\<T>  |                          |               |
| limt                                       | Stream\<T>  | long                     |               |
| skip                                       | Stream\<T>  | long                     |               |
| takeWhile                                  | Stream\<T>  | Predicate\<T>            | T->boolean    |
| dropWhile                                  | Stream\<T>  | redicate\<T>             | T->boolean    |

### 1.1. filter

```java
Stream<T> filter(Predicate<? super T> predicate)
```

파라미터로 받은 조건에 따라 긴지 아닌 판단 (`predicate`)하여 조건에 맞는 대상만 추출해서 새로운 스트림을 리리턴하는 메서드  Returns a stream consisting of the elements of this stream that match the given predicate.

* Parameters:`predicate` - a [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) predicate to apply to each element to determine if it should be included (각각의 요소를 포함해야할지 말지를 판단)



### 1.2. map

```java
<R> Stream<R> map(Function<? super T,? extends R> mapper)
```

파라미터로 받은 fn을 이용해서 반환된 결과를 새로운 스트림으로 리턴하는 메서드. Returns a stream consisting of the results of applying the given function to the elements of this stream.

* Type Parameters:`R` - The element type of the new stream (리턴할 스트림의 타입을 의미함. 매핑결과가 타입이 달라질 수 있기 때문에 이렇게 선언함)&#x20;
* Parameters:`mapper` - a [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) function to apply to each element (각각의 요소에 대응되는 함수)



### 1.3. flatMap

```java
<R> Stream<R> flatMap(Function<? super T,? extends Stream<? extends R>> mapper)
```

Returns a stream consisting of the results of replacing each element of this stream with the contents of a mapped stream produced by applying the provided mapping function to each element. Each mapped stream is [`closed`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/BaseStream.html#close--) after its contents have been placed into this stream. (If a mapped stream is `null` an empty stream is used, instead.)

* API Note:The `flatMap()` operation has the effect of applying a one-to-many transformation to the elements of the stream, and then flattening the resulting elements into a new stream.
* Type Parameters:`R` - The element type of the new stream
* Parameters:`mapper` - a [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) function to apply to each element which produces a stream of new values



### 1.4. sorted

```java
Stream<T> sorted(Comparator<? super T> comparator)
```

Returns a stream consisting of the elements of this stream, sorted according to the provided `Comparator`. For ordered streams, the sort is stable. For unordered streams, no stability guarantees are made.

* Parameters:`comparator` - a [non-interfering](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#NonInterference), [stateless](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness) `Comparator` to be used to compare stream elements



## 2. Terminal operation (최종연산)&#x20;

| Operation | Return type   | Functional IF                                                                                    | Fn discriptor |
| --------- | ------------- | ------------------------------------------------------------------------------------------------ | ------------- |
| forEach   | void          | Consumer\<T>                                                                                     | T-> void      |
| collect   | R             | [collector-less-than-t-a-r-greater-than.md](collector-less-than-t-a-r-greater-than.md "mention") |               |
| reduce    | Optional\<T>  | BinaryOperator\<T>                                                                               | (T,T) -> T    |
| anyMatch  | boolean       | Predicate\<T>                                                                                    | T -> boolean  |
| noneMatch | boolean       | Predicate\<T>                                                                                    | T -> boolean  |
| allMatch  | boolean       | Predicate\<T>                                                                                    | T -> boolean  |
| count     | ong (generic) | long                                                                                             |               |
| findAny   | Optional\<T>  |                                                                                                  |               |
| findFirst | Optional\<T>  |                                                                                                  |               |

### 2.1. forEach

### 2.2. collect
