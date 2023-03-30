---
description: java.util.function.Consumer (표준 API에서 이미 제공)
---

# Consumer\<T>

제네릭 형식 T 객체를 받아서 void를 반환하는 accept라는 추상 메서드 정의. T형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 Consumer 인터페이스를 사용함.&#x20;

```java
package java.util.function;
import java.util.Objects;

@FunctionalInterface
public interface Consumer<T> {
    void accept(T t); // T를 받아서 소모(void 아무것도 리턴하지 않음)하는 추상 메서드 
    
    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

{% hint style="info" %}
Consumer 인터페이스형을 인수로 가진 Stream interface 메서드

* forEach()
* peek()
{% endhint %}

```java

public <T> void forEach(List<T> list, Consumer<T> c) {
    for (T t : list) {
        c.accept(t);
    }
}
forEach(
    Array.asList(1,2,3,4,5), (integer i) -> System.out.println(i)
);
```



#### 특정형식을 입력으로 받는 함수형 인터페이스&#x20;

* IntConsumer
* LongConsumer
* DoubleConsumer
