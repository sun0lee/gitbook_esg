---
description: java.util.stream.Stream 인터페이스
---

# Stream Operations ; 연산

<figure><img src="../../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

|             intermediate operation ; 중간연산              |          terminal operation ; 최종연산          |
| :----------------------------------------------------: | :-----------------------------------------: |
|                     연결할 수 있는 스트림연산                     |                 스트림을 닫는 연산                  |
| 하나의 스트림을 다른 스트림으로 변환하는 연산, 스트림의 요소를 소비하지 않음. 파이프라인 구성  |          스트림의 요소를 소비하여 최종결과를 도출함.           |
|                  filter, sorted, map                   | count, findFirst, forEach, reduce, collect  |
|                                                        |                                             |

## Intermediate operation (중간연산)&#x20;

| Operation | Return type | Functional IF            | Fn discriptor |
| --------- | ----------- | ------------------------ | ------------- |
| filter    | Stream\<T>  | Predicate\<T>            | T -> boolean  |
| map       | Stream\<R>  | Function\<T,R>           | T -> R        |
| flatMap   | Stream\<R>  | Function\<T, Stream\<R>> | T->Stream\<R> |
| sorted    | Stream\<T>  | Comparator\<T>           | (T,T) -> int  |
| distinct  | Stream\<T>  |                          |               |
| limt      | Stream\<T>  | long                     |               |
| skip      | Stream\<T>  | long                     |               |
| takeWhile | Stream\<T>  | Predicate\<T>            | T->boolean    |
| dropWhile | Stream\<T>  | redicate\<T>             | T->boolean    |

## Terminal operation (최종연산)&#x20;

| Operation | Return type    | Functional IF                                                                                    | Fn discriptor |
| --------- | -------------- | ------------------------------------------------------------------------------------------------ | ------------- |
| forEach   | void           | Consumer\<T>                                                                                     | T-> void      |
| count     | long (generic) | long                                                                                             |               |
| collect   | R              | [collector-less-than-t-a-r-greater-than.md](collector-less-than-t-a-r-greater-than.md "mention") |               |
| reduce    | Optional\<T>   | BinaryOperator\<T>                                                                               | (T,T) -> T    |
| anyMatch  | boolean        | Predicate\<T>                                                                                    | T -> boolean  |
| noneMatch | boolean        | Predicate\<T>                                                                                    | T -> boolean  |
| allMatch  | boolean        | Predicate\<T>                                                                                    | T -> boolean  |
| findAny   | Optional\<T>   |                                                                                                  |               |
| findFirst | Optional\<T>   |                                                                                                  |               |

