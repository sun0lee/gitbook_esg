# Method References

## Method References ; 메서드 참조

기존의 메서드 정의를 재활용해서 람다([lambdas](lambdas/ "mention"))처럼 전달하는 방식. 특정 매서드만을 호출하는 람다의 축약형이라고 생각할 수 있음.&#x20;

메서드를 참조해서 매개변수의 정보 및 리턴 타입을 알아내 람다식에서 불필요한 매개변수를 제거하는 것을 목적으로 함. &#x20;

* 인스턴스 메서드일 경우에는 먼저 객체를 생성한 다음 **참조변수 :: 인스턴스 메서드명**&#x20;

{% code title="참조변수 :: 메서드 " overflow="wrap" %}
```java
inventory.sort(comparing(Apple::getWeight)); 
//Apple 클래스의 getWeight 메서드를 참조하기 
```
{% endcode %}

{% code title="참조변수 :: 메서드 " %}
```java
d -> d.getName() // 람다 표현식 
Dish::getName // 메서드 참조 
```
{% endcode %}

* &#x20;static 정적 메서드를 참조하는 경우 객체생성이 불필요하므로 **클래스명 :: 메서드명**&#x20;

{% code title="클래스 :: 메서드 " %}
```java
(left, right) -> Math.max(left, right);
Math ::max ;
```
{% endcode %}

{% code title="클래스 :: 메서드 " %}
```java
Str -> System.out.println(str); //람다 표현식 
System.out::println; // 메서드 참조 
```
{% endcode %}

## Constructor References ; 생성자 참조

클래스 명과 new 키워드를 이용해서 기존 생성자 참조를 만들 수 있음. 생성자 역시 특수한 메서드 중의 하나이기 때문에 동일하게 생략할 수 있음.&#x20;

{% code title="람다식 " %}
```java
(a,b) ->{return new 클래스(a,b) ; }
```
{% endcode %}

{% code title="생성자 참조 " %}
```java
class :: new
```
{% endcode %}

