---
description: java.util.function.Predicate (표준 API에서 이미 제공)
---

# Predicate\<T>

제네릭 형식의 T의 객체를 인수로 받아 boolean을 리턴하는 test라는 추상 메서드를 정의.&#x20;

```java
package java.util.function;
import java.util.Objects;

@FunctionalInterface
public interface Predicate<T> { // T객체를 인수로 받아서 
    boolean test(T t);   // 추상메서드 : boolean을 리턴함. 

// 디폴트 메서드가 여러개여도 상관없음 추상메서드가 오직 test(T t) 1개  
    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }
    default Predicate<T> negate() {
        return (t) -> !test(t);
    }
    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }
    static <T> Predicate<T> isEqual(Object targetRef) {
        return (null == targetRef)
                ? Objects::isNull
                : object -> targetRef.equals(object);
    }
    @SuppressWarnings("unchecked")
    static <T> Predicate<T> not(Predicate<? super T> target) {
        Objects.requireNonNull(target);
        return (Predicate<T>)target.negate();
    }
}
```

{% hint style="info" %}
Predicate 인터페이스형을 인수로 가진 Stream interface 메서드&#x20;

* allMatch()
* anyMatch()
* nonMatch()
{% endhint %}

<pre class="language-java"><code class="lang-java">// 문자열이 들어오면 비어있는지 판단해주는 람다를 정의  
<a data-footnote-ref href="#user-content-fn-1">Predicate</a>&#x3C;String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
 
// 정의한 람다식을 이용해서 문자열이 비어있는지 체크 
List&#x3C;String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate) ;
</code></pre>

#### 특정형식을 입력으로 받는 함수형 인터페이스

* IntPredicate
* LongPredicate
* DoublePredicate

[^1]: Predicate 인터페이스를 구현함&#x20;
