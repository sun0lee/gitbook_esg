---
description: Interface Stream<T> / (Package) java.util.stream
---

# Stream

## 1. Definition&#x20;

스트림이란 컬렉션의 저장요소를 하나씩 참조해서 람다표현식([lambdas.md](../behavior-parameterization/lambdas.md "mention"))으로 처리할 수 있도록 해주는 반복자

&#x20;데이터 처리 연산을 지원하도록(supporting sequential and parallel aggregate operations) 소스에서 추출된 연속된 요소 (A sequence of elements) .

* `Stream<T>` 인터페이스(자바 8에서 추가된 스트림 API에서 핵심적인 역할을 수행)&#x20;
* 요소의 원본 데이터 소스를 변경하지 않는 비상태적인 계산을 수행함&#x20;
* `Stream<T>` 인터페이스는 함수형 프로그래밍 스타일을 지원하기 위해 설계되었으며, 람다식과 함수형 인터페이스를 활용함.&#x20;
* `Stream<T>` 인터페이스는 제네릭 타입 `T`를 가지며, 이는 스트림에서 처리할 요소의 타입을 나타냄&#x20;
* `Stream<T>` 인터페이스는 여러가지 메서드를 제공하여 요소를 처리하고 스트림 연산을 수행할 수 있음.&#x20;
  * [#intermediate-operation](stream-operations/#intermediate-operation "mention"): 스트림에서 요소를 추출하거나 변환하는 등의 연산을 수행하며, 연속적으로 적용될 수 있음.
  * [#terminal-operation](stream-operations/#terminal-operation "mention") : 스트림에서 추출된 요소들에 대한 최종 처리를 수행하며, 스트림을 닫음.&#x20;



* (참고사항) 자바 8 컬렉션에는 스트림을 반환하는 Stream 메서드가 추가됨. 즉 컬렉션에서도 스트림을 활용하여 여러가지 작업을 할 수 있다는 소리임.&#x20;
  * 스트림을 이용하면 선언형으로 컬렉션 데이터를 처리할 수 있음. (데이터 컬렉션 반복을 멋지게 처리하는 기능)&#x20;

{% code overflow="wrap" %}
```java
List <String> list Array.asList("M001","M002","M003") ;
// 스트림으로 변경하면 
Stream <String> stream = list.stream() ;
// 컬렉션 (java.util.Collecntion)의 stream() 메소드로 스트림객체를 얻고 나서, 
stream.forEach (matCd -> System.out.println(matCd));
//메소드를 통해 컬렉션의 요소를 하나씩 콘솔에 출력. 
```
{% endcode %}

## 2. 스트림의 특징&#x20;

* pipelining : 파이프 라인
* lazy : 게으른 연산



## &#x20;3. Collection vs. Stream&#x20;

Collection은 자료 자체를 정의하고 담고 처리하는 방식에 집중하는 반면 Stream은 대상을 가지 처리할 동작 자체에 관심이 있음.&#x20;

|                        Collection                       |                   Stream                   |
| :-----------------------------------------------------: | :----------------------------------------: |
|               자료구조, 요소의 저장 및 접근 연산이 주를 이룸               |                 계산식이 주를 이룸                 |
|                      데이터 중심 (data)                      |                 계산식 중심 (do)                |
|           현재 자료구조가 포함하는 모든 값을 메모리에 저장하는 자료구조.           | 요청할 때만 계산하는 고정된 자료구조. 요소를 추가하거나 삭제할 수 없음.  |
|                       어떻게 할 것인가 ?                       |                 무엇을 하고싶은가 ?                |
| 컬렉션의 모든 요소는 컬렉션에 포함되기 전에 저장됨. 전체를 메모리에 띄운 뒤에 뭔가를 시작함.   | 스트림 =게으르게 만들어지는 컬렉션. 그때 그때 지나가는 대상만을 처리함.  |
|          외부반복 ; 반복에 대한 행위를 명시적으로 정의해서 지정해줘야 함.          |              내부 반복 ; 알아서 해줌.               |

{% embed url="https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/util/stream/Stream.html" %}
