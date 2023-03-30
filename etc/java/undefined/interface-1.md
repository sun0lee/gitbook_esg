# Interface

## Interface

여러 프로그램에서 사용할 멤버 (변수, 메서드)를 일관되게 하기 위한 기술명세. 다른 클래스에서 구현할 내용에 대해 형식만 갖춘 것.&#x20;

* public static final 필드
* public abstract 메서드&#x20;

인터페이스는 추상 클래스의 한 종류이다. 인터페이스의 &#x20;

* 모든 필드와 메서드는 public -> 누구나 접근 가능.&#x20;
* 모든 필드는 static -> 인스턴스 생성과 상관없이 사용할 수 있는 static으로 선언
* 모든 필드는 final -> 초기화된 값을 변경할 수 없음. &#x20;
* 모든 메서드는 abstract -> 직접 구현하지 않음&#x20;

인터페이스는 일반적인 추상클래스와는 **달리** 다른 클래스가 상속받을 때 extends가 아닌 <mark style="color:blue;">**implement**</mark> 키워드를 사용하는데 이는 **여러 인터페이스를 대상으로 지정**할 수 있다. 다중상속이 가능함. &#x20;

인터페이스에 한정하여 다중 상속을 허용하며 일반적인 추상 클래스의 경우 추상 메서드와 일반 메서드를 함께 정의할 수 있지만 인터페이스의 경우 추상 메서드만 정의할 수 있다는 차이가 있다.

{% embed url="https://blog.naver.com/ahfkenehla/222663513619" %}

## Default Method&#x20;

기존의 인터페이스는 추상 메서드만 존재할 수 있고, 이를 상속받는 구현체에서 직접 해당 추상 메서드를 구현하도록 되어 있음. &#x20;

Default Method는 인터페이스에 있는 구현 메서드를 의미함. ( @since java8, interface개념에 디폴트 메서드를 사용할 수 있게 됨.)

이미 공개된 인터페이스에 추상 메서드를 추가하게 되면 기존의 모든 구현체에서 추가된 추상 메서드를 구현을 해야하는 일이 발생함. 이런 수고로움을 처리하기 위해 (= 이미 공개된 인터페이스의 변경을 쉽게 할수 있도록) default method 개념이 추가됨. ( $$\because$$_**OCP**_ Open-Close-Principle ; 확장에 개방되어 있고, 변경에 닫혀있는 코드 설계 원칙)

* default method 조건&#x20;
  * 메서드 앞에 예약어 `default`
  * 구현부 `{ }` 필수 !!&#x20;

```java
public interface Predicate<T> { 
    boolean test(T t); //추상 메서드 ; 여기서 구현 안함!!  

    // 디폴트 메서드 ; 구현이 interface에 되어있음  
    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }
    // 디폴트 메서드 
    default Predicate<T> negate() {
        return (t) -> !test(t);
    }
    ...
}
```

디폴트 메서드는 인터페이스에 추상 메서드가 추가되는 경우 구현 클래스에서 코드를 구현할 필요가 없도록 (못하게 하는 것은 아님) 생성된 개념. 그래서 필요에 따라(충돌 발생 시) 구현 클래스에서 default method를 재정의할 수 있음.&#x20;

{% embed url="https://velog.io/@heoseungyeon/%EB%94%94%ED%8F%B4%ED%8A%B8-%EB%A9%94%EC%84%9C%EB%93%9CDefault-Method" %}

static 메서드 선언 가능 -> since 자바8&#x20;

privata 메서드 선언 기능 -> since 자바 9&#x20;
