---
description: 동작 파라미터화
---

# Behavior parameterization

## 함수형 프로그래밍&#x20;

선언형 프로그래밍의 일환인 함수형 프로그래밍.&#x20;

함수형 프로그래밍은 순수 함수를 이용한 프로그래밍 패러다임으로, 입력값이 같으면 항상 같은 결과를 반환하는 함수를 이용하여 프로그래밍하는 것을 의미함. 함수형 프로그래밍은 불변성, 데이터 흐름의 제어, 추상화 등의 개념을 중요시하며, 이를 통해 코드의 가독성과 유지보수성을 높일 수 있음.

_**How (어떻게 하는지)**_에 치중하는 명령형 프로그래밍과 달리 _**What(무엇을 하는가)**_에 집중하도록 함. 코드의 내용을 파악하기 훨씬 쉽고 직관적임.&#x20;

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

#### &#x20;함수형  means .. "함수", "if-then-else" 등 수학적 표현만 사용하는 방식

수학의 함수처럼 부작용이 없는 함수형 프로그래밍 , 시스템의 다른 부분에 영향을 미치지 않는다면 내부적으로 함수형이 아닌 기능도 사용하는것을 허용함.&#x20;

함수나 메서드는 지역 변수만 변경함. 함수나 메서드에서 참조하는 객체가 있다면 그것은 불변 객체이어야 함. 예외적으로 생성한 객체의 필드는 갱신할 수 있으나, 외부에 노출되지 않아야 함. 다음 번 메서드 호출 결과에 영향을 미치지 않아야 함. -> 함수나 메서드가 어떤 경우에도 예외를 일으키지 않음.&#x20;

* 함수형 프로그래밍은 객체의 상태에 따라 값이 달라지지 않는 속성을 띄게 함. 이는 수학에서처럼 함수의 함수를 이용하는 등 활용성이 높아질 수 있음.&#x20;
* 또한 성능차원에서 병렬처리, 내부반복 등 연산처리과정에서 최적화를 용이하게 함. (locking 불필요)&#x20;

## 동작 파라미터화&#x20;

자바에서 함수형 프로그래밍의 노력? 혹은 기조 변화의 결과물로 동작 파라미터화가 있음. 동작 파라미터화는 메서드를 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달하는 방식.&#x20;

동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달함. 일례로 스트림 API도 연산의 동작을 파라미터화 할 수 있는 코드를 전달한다는 사상에 기초함.&#x20;

이러한 작업이 가능하도록, 혹은 수월하게 구현할 수 있도록 자바 8부터 아래의 기능들이 추가 & 보완됨.&#x20;

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><p>람다식</p><p><em><strong>Lambdas</strong></em> </p></td><td></td><td><a href="lambdas/">lambdas</a></td></tr><tr><td></td><td>메서드 참조 </td><td><em><strong>Method References</strong></em></td><td><a href="method-references.md">method-references.md</a></td></tr><tr><td></td><td>함수형 인터페이스 </td><td><em><strong>Functional Interface</strong></em> </td><td><a href="functional-interface/">functional-interface</a></td></tr></tbody></table>

&#x20; &#x20;
