---
description: java.util.function.Function<T>
---

# Function\<T,R>

제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 apply를 정의함. 입력을 출력으로 매핑하는 람다를 정의할 때 Function 인터페이스를 활용할 수 있음. (요소변환)&#x20;

```java
package java.util.function;
import java.util.Objects;

@FunctionalInterface
public interface Function<T, R> {
    R apply(T t); // T를 받아서 R을 리턴하는 추상메서드 (타입이 달라질 수 있음)
    
    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }
    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }
    static <T> Function<T, T> identity() {
        return t -> t;
    }
}
```

{% hint style="info" %}
&#x20;Function 인터페이스형을 인수로 가진 Stream interface 메서드

* map()
* flatMap()
{% endhint %}

{% code overflow="wrap" %}
```java
public <T,R> List<R> map(List<T> list, Function<T,R> f ){
    List<R> result = new ArrayList<>() ; // 결과 값 담을 통 
    for(T t :list) {
        result.add(f.apply(t)); // 람다식 call 
    }
    return result ;
}

List<Integer> l = map (
    Arrays.asList("lambdas","in","action"), (Sting s) -> s.length() 
    // 람다식, function의 apply를 구현함. 
    // String을 인수로 받아서 int를 리턴함. => 인수와 리턴의 타입이 다름 !! 
) ;
```
{% endcode %}

#### 특정형식을 입력으로 받는 함수형 인터페이스&#x20;

* IntFunction
* LongFuntion
* DoubleFuntion

#### 특정 형 변환 (참조형 -> Primitive )

* ToIntFunction\<T>
* ToLongFuntion&#x20;
* ToDoubleFuntion&#x20;

#### 특정 형 변환 (Primative -> Primitive)

* IntToDoubleFunction
* IntToLongFuntion
* LongToIntFunction
* LongToDoubleFuntion
* DoubleToIntFuntion
* DoubleToLongFuntion

## 인수를 두 개 받는 Function 인터페이스

### BiFunction\<T,U,R>

* 함수 디스트립터 (T,U) -> R&#x20;

#### 특정 형식을 입력으로 받는 함수형 인터페이스&#x20;

* ToIntBiFunction\<T,U> ; 리턴값이 int라는 뜻임.
* ToLongBiFunction\<T,U>
* ToDoubleBiFunction\<T,U>















