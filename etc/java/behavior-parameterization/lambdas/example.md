# 람다식 example

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
