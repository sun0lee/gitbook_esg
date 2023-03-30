---
description: '함수형 인터페이스, java.util.function 표준 API  #람다식 #제네릭'
---

# Functional Interface

_**SAM(Single Abstract Method), 오직 하나의 추상 메서드만 정의하는 인터페이스**를 함수형 인터페이스라고 함. **자바의 람다 표현식은 함수형 인터페이스로만 가능함.**  (함수형 인터페이스의 추상 메서드는 람다 표현식의 시그니처를 묘사)_

미구현 메서드 하나만 구현하면 객체를 생성할 수 있는 형태의 추상 클래스. 익명 내부 클래스보다 간편한 형태로, 함수를 구현하여 객체를 생성할 수 있음.

함수형 인터페이스를 매번 선언하고 구현하는 불편함을 덜어주기 위해 java 8부터 제공. 이름 그대로 모두 interface로 구성되어 있고 제네릭을 사용하여 일관성 있고, 편리한 함수형 프로그램을 가능하게 함. 이 인터페이스들의 목적은 메서드 또는 생성자 매개 타입으로 사용되어 람다식을 대입할 수 있도록 하기 위함임.

<details>

<summary>function descriptor </summary>

람다 표현식의 signature를 서술하는 메서드; 함수형 인터페이스의 추상 메서드 시그니처 (function의 특성을 분류하기 위한 구분자 같은거라고 해석됨.)

* () -> void ; 인수와 반환값이 없는 시그니처&#x20;
* (Apple, Apple) -> int ; 두 개의 Apple을 인수로 받아 int를 반환하는 함수&#x20;

</details>

&#x20;

|                                     FunctionalInterface                                    |    메서드   |    함수 디스크립터   | Primitive specializations                                                 |
| :----------------------------------------------------------------------------------------: | :------: | :-----------: | ------------------------------------------------------------------------- |
|   [consumer-less-than-t-greater-than.md](consumer-less-than-t-greater-than.md "mention")   | accept() |   T -> void   | IntConsumer, LongConsumer, DoubleConsumer                                 |
|   [supplier-less-than-t-greater-than.md](supplier-less-than-t-greater-than.md "mention")   |   get()  |    () -> T    | BooleanSupplier, IntSupplier, LongSupplier, DoubleSupplier                |
| [function-less-than-t-r-greater-than.md](function-less-than-t-r-greater-than.md "mention") |  apply() |     T -> R    | IntFunction\<R>, IntToDoubleFunction, IntToLongFunction, LongFunction\<R> |
|   [operator-less-than-t-greater-than.md](operator-less-than-t-greater-than.md "mention")   |  apply() |     T -> T    |                                                                           |
|  [predicate-less-than-t-greater-than.md](predicate-less-than-t-greater-than.md "mention")  |  test()  | T -> boolean  | IntPredicate, LongPredicate, DoublePredicate                              |





[consumer-less-than-t-greater-than.md](consumer-less-than-t-greater-than.md "mention")

* 입력을 받아서 그것을 소비하는 함수적 인터페이스&#x20;
* accept() 메서드 : 매개값을 소비하는 역할을 함. 사용하고 리턴값 없음.&#x20;

[supplier-less-than-t-greater-than.md](supplier-less-than-t-greater-than.md "mention")

* 실행 후 호출한 곳으로 데이터를 리턴(공급)하는 역할을 하는 함수적 인터페이스&#x20;
* 매개변수가 없고 리턴값이 있는 get머시기() 메서드를 가지고 있음.&#x20;

[function-less-than-t-r-greater-than.md](function-less-than-t-r-greater-than.md "mention")

* 매개값 리턴값이 있는 apply머시기() 메서드를 가지고 있음.
* 매개값을 리턴값으로 매핑 (타입변환) 하는 역할을 함. &#x20;

[operator-less-than-t-greater-than.md](operator-less-than-t-greater-than.md "mention")

* 매개값과 리턴값이 있는 apply머시기() 메서드를 가지고 있음.&#x20;
* 매개값과 리턴값의 타입이 같을 때, 즉 동일한 타입. -> function과 차이점 !!&#x20;
* 매개값을 이용하여 연산하고 매개값과 동일 타입을 리턴하는 역할을 함.&#x20;

[predicate-less-than-t-greater-than.md](predicate-less-than-t-greater-than.md "mention")

* 매개변수와  boolean (; true, false) 리턴값이 있는 test머시기() 메서드를 가지고 있음.&#x20;
* 매개값을 조사해서 true, false를 리턴하는 역할을 함. &#x20;

{% embed url="https://bcp0109.tistory.com/313" %}
