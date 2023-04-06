# Lambdas

## Lambdas ; 람다 표현식&#x20;

메서드로 전달할 수 있는 익명함수를 단순화한 것.

파라메터,  화살표 -> , 바디로 구성된 단순화된 익명(이름없음) 함수(특정 클래스에 종속x). 이름이 없지만 메서드처럼 파라미터, 리스트, 바디, 반환형식, 발생 가능한 예외 리스트를 가질 수 있음.



$$\overbrace{\big(\sf Apple\  a1,Apple\  a2\big)}^{\text{람다 파라미터}}\overbrace{ \ \text{->} \ }^{\text{화살표}} \overbrace{ \sf a1.getWeight().compareTo(a2.getWeight());}^{\text{람다 바디}}$$



* expression style `(parameter) -> expression`&#x20;
* block style `(parameter) -> { statements; }`

<details>

<summary>example</summary>

```java
//객체 생성 
()-> new Apple(10)
```

{% code overflow="wrap" %}
```java
//객체에서 소비
 (Apple a) -> {System.out.println(a.getWeighr());
```
{% endcode %}

{% code overflow="wrap" %}
```java
//객체에서 선택,추출
 (String s)->s.length()
```
{% endcode %}

{% code overflow="wrap" %}
```java
//두 값을 조합 
(int a, int b)-> a * b
```
{% endcode %}

{% code overflow="wrap" %}
```java
//두 객체 비교 
(Apple a1, Apple a2)-> a1.getWeight().compareTo(a2.getWeight())
```
{% endcode %}

</details>

> 함수형 인터페이스([functional-interface](functional-interface/ "mention"))라는 문맥에서 람다 표현식을 사용할수 있다 ?&#x20;
>
> * 람다 표현식은 변수에 할당하거나 함수형 인터페이스를 인수로 받는 메서드로 전달할 수 있음.
> * 함수형인터페이스의 추상 메서드와 같은 시그니처를 갖는다. &#x20;
> * 함수형인터페이스를 인수로 받는 메서드에만 람다 표현식을 사용할 수 있음&#x20;
> * 람다 표현식을 이용해서 함수형 인터페이스의 추상 메서드를 즉석으로 제공할 수 있으며 람다 표현식 전체가 함수형 인터페이스의 인스턴스로 취급됨.&#x20;

<details>

<summary>함수형 인터페이스, 람다식, 익명클래스 </summary>

{% code title="함수형 인터페이스 " %}
```java
Package java.util.function ;

@FunctionalInterface 
Public interface IntBinaryOperator {
    Int applyAsInt(int x, int y);
```
{% endcode %}

{% code title="람다식으로 구현 " %}
```java
 IntBinaryOperator op = (x,y) -> x+y ;

// 변수 op 가 IntBinaryOperator 함수형 인터페이스형
// IntBinaryOperator op = new IntBinaryOperator(){...; } 
// 익명클래스를 구현한 것임을 추론.
// 추상메소드의 구현도 약속됨. 
```
{% endcode %}

{% code title="익명 클래스 이용 " %}
```java
// 람다식의 형을 추론함. 
IntBinaryOperator op = new IntBinaryOperator(){
    public int applyAsInt(int x, int y){
      Return x+y ;
    }
}
```
{% endcode %}

</details>

