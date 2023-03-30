---
description: Interface Stream<T> / (Package) java.util.stream
---

# Stream (API)

자바 8 컬렉션에는 스트림을 반환하는 Stream 메서드가 추가됨.

스트림이란 컬렉션의 저장요소를 하나씩 참조해서 람다표현식([lambdas](../behavior-parameterization/lambdas/ "mention"))으로 처리할 수 있도록 해주는 반복자

&#x20;데이터 처리 연산을 지원하도록 source에서 추출된 연속된 요소  A sequence of elements supporting sequential and parallel aggregate operations.

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

Stream은 Iterator와 비슷한 역할을 하는 반복자지만,  람다표현식([lambdas](../behavior-parameterization/lambdas/ "mention"))으로 요소 처리 코드를 제공하며, 내부 반복자를 사용하여 병렬처리가 쉬움.&#x20;

스트림을 이용하면 선언형으로 컬렉션 데이터를 처리할 수 있음. (데이터 컬렉션 반복을 멋지게 처리하는 기능)&#x20;

* pipelining&#x20;
  * 스트림끼리 연결해서 커다란 파이프라인을 구성할 수 있도록 스트림 자신을 반환함.
  * builder patten과 유사함. (.으로 연결\~ 연결\~)&#x20;
* 내부반복&#x20;

## &#x20;Collection vs. Stream&#x20;

|                        Collection                       |                   Stream                   |
| :-----------------------------------------------------: | :----------------------------------------: |
|               자료구조, 요소의 저장 및 접근 연산이 주를 이룸               |                 계산식이 주를 이룸                 |
|                      데이터 중심 (data)                      |                 계산식 중심 (do)                |
|           현재 자료구조가 포함하는 모든 값을 메모리에 저장하는 자료구조.           | 요청할 때만 계산하는 고정된 자료구조. 요소를 추가하거나 삭제할 수 없음.  |
|                       어떻게 할 것인가 ?                       |                 무엇을 하고싶은가 ?                |
| 컬렉션의 모든 요소는 컬렉션에 포함되기 전에 저장됨. 전체를 메모리에 띄운 뒤에 뭔가를 시작함.   | 스트림 =게으르게 만들어지는 컬렉션. 그때 그때 지나가는 대상만을 처리함.  |
|          외부반복 ; 반복에 대한 행위를 명시적으로 정의해서 지정해줘야 함.          |              내부 반복 ; 알아서 해줌.               |

Collection은 자료 자체를 정의하고 담고 처리하는 방식에 집중하는 반면 Stream은 대상을 가지 처리할 동작 자체에 관심이 있음.&#x20;

{% embed url="https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/util/stream/Stream.html" %}
