---
description: java.util.function.Supplier
---

# Supplier\<T>

인자(매개변수)를 받지 않고 뭔가를 반환하는 역할을 함. (be used as the assignment target for a lambda expression or method reference.)&#x20;

```java
package java.util.function;

@FunctionalInterface
public interface Supplier<T> {
    T get(); // 아무것도 받지 않고 뭔가를 주는(return) 추상 메서드 
}
```

{% hint style="info" %}
Supplier 인터페이스형을 인수로 가진 Stream interface 메서드&#x20;

* generate()
* collect()
{% endhint %}

```java
Supplier supplier = ()-> new Apple(10) ;
```

```java
Supplier supplier = ()-> Math.random(); // 람다식

Supplier<Double> s = Math::random ;  // 메서드 참조  
```

```java
IntSupplier insSupplier = ()->{
    return (int) ( math.random()*6+1 ; )
} ;
```

#### 특정형식을 입력으로 받는 함수형 인터페이스

* BooleanSupplier
* IntSupplier
* LongSupplier
* DoubleSupplier&#x20;



