---
description: 자료구조
---

# Collection (framework)

일반적인 자료구조를 바탕으로 객체를 효율적으로 추가, 삭제, 검색할 수 있도록 java.util 패키지에 컬렉션 관련된 인터페이스 클래스를 포함시킴. 자바에서 컬렉션 프레임워크(collection framework)란 다수의 데이터를 쉽고 효과적으로 처리할 수 있는 표준화된 방법을 제공하는 클래스의 집합을 의미.

즉, 데이터를 저장하는 자료 구조와 데이터를 처리하는 알고리즘을 구조화하여 클래스로 구현해 놓은 것.

컬렉션 : 요소를 수집해서 저장하는 것 ; 객체를 수집해서 저장하는 역할&#x20;

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

## java.lang.Iterable

<details>

<summary>Iterable 인터페이스 메서드</summary>

```java
public interface Iterable<T> {

    // T 타입의 Iterator 생성 
    Iterator<T> iterator();
    
    // 컬렉션의 모든 데이터에 대해 지정된 명령문을 실행 
    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }
    // 병렬처리할 수 있는 spliterator 생성 
    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
}
```

</details>

## java.util.Collection&#x20;

<details>

<summary>Iterable 인터페이스 메서드</summary>

{% code title="Collection 인터페이스의 메서드 " %}
```java
public interface Collection<E> extends Iterable<E> {
  // 컬렉션의 요소 갯수 
  int size();
  
  // 컬렉션의 요소 존재여부 판단 
  boolean isEmpty();
  
  // 매개변수로 전달받은 객체의 컬렉션 포함여부 판단 
  boolean contains(Object o);

  // 
  Iterator<E> iterator();
  
  // 컬렉션의 요소들을 가지는 Object 타입의 배열 생성 
  Object[] toArray();

  // 컬렉션의 요소들을 가지는 T 타입의 배열 생성 
  <T> T[] toArray(T[] a);

  //
  default <T> T[] toArray(IntFunction<T[]> generator) {
      return toArray(generator.apply(0));
  }
 
  // 매개변수로 전달받은 객체를 컬렉션에 추가 
  boolean add(E e);

  // 매개변수로 전달받은 객체를 컬렉션에서 삭제 
  boolean remove(Object o);

  // 매개변수로 전달받은 컬렉션 요소들의 컬렉션 포함여부 판단 
  boolean containsAll(Collection<?> c);

  // 매개변로 전달받은 컬렉션의 요소들을 컬렉션에 추가 
  boolean addAll(Collection<? extends E> c);

  // 매개변수로 전달받은 컬렉션 요소들을 현재 컬렉션에서 삭제  
  boolean removeAll(Collection<?> c);

  // 매개변수로 전달받은 조건에 해당하는 요소 삭제. 
  default boolean removeIf(Predicate<? super E> filter) {
      Objects.requireNonNull(filter);
      boolean removed = false;
      final Iterator<E> each = iterator();
      while (each.hasNext()) {
          if (filter.test(each.next())) {
              each.remove();
              removed = true;
          }
      }
      return removed;
  }

  // 매개변수로 전달받은 컬렉션의 요소들만 남기고 나머지는 모두 삭제 
  boolean retainAll(Collection<?> c);

  // void 컬렉션의 모든 요소 삭제 ??
  void clear();

  // 매개변수로 전달받은 객체와 현재 컬렉션의 동일여부 판단 
  boolean equals(Object o);
  
  // 현재 컬렉션의 해시코드 반환 
  int hashCode();

  @Override
  default Spliterator<E> spliterator() {
      return Spliterators.spliterator(this, 0);
  }

  // 컬렉션을 소스로 하는 스트림 생성 
  default Stream<E> stream() {
      return StreamSupport.stream(spliterator(), false);
  }

  default Stream<E> parallelStream() {
      return StreamSupport.stream(spliterator(), true);
  }
}

```
{% endcode %}

</details>

### [list-less-than-e-greater-than.md](list-less-than-e-greater-than.md "mention")

순서를 유지하고 저장, 중복저장가능&#x20;

* ArrayList
* Vector
* Stack
* LinkedList

### [set-less-than-e-greater-than.md](set-less-than-e-greater-than.md "mention")

순서를 유지하지 않고 저장, 중복저장 불가

* HashSet
* SortedSet
  * TreeSet

## java.util.Map

### [map-less-than-k-v-greater-than.md](map-less-than-k-v-greater-than.md "mention")

키와 값을 하나의 쌍으로 묶어서 관리하는 구조.&#x20;

* HashMap
* Hashtable
* SortedMap
  * TreeMap

<details>

<summary>&#x3C;> Generic ; 제네릭</summary>

제네릭은 클래스와 인터페이스, 메소드를 정의할 때 타입을 파라미터로 사용할 수 있도록 해줌.&#x20;

데이터 타입(data type)을 일반화(generalize) 한다는 것을 의미. 데이터 형식에 의존하지 않고, 하나의 값이 여러 다른 데이터 타입을 가질 수 있도록 하는 방법&#x20;

외부 클래스에서 제네릭 클래스를 생성할 때 <> 괄호 안에 타입을 파라미터로 보내 제네릭 타입을 지정해주는 것. (클래스 내부에서 지정하는 것이 아닌 외부에서 사용자에 의해 지정)

* 국룰) 타입 파라미터는 변수명과 동일한 규칙에 따라 작성가능하나, 일반적으로 알파벳 1개 글자를 사용함.
  * \<T> Type
  * \<E> Element
  * \<K> Key
  * \<V> Value
  * \<N> Number
  * \<?> wild card ; 뭐가 들어오든 상관 없음

클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정하는 방법 (컴파일 전에 미리 타입검사를 수행함.)

* 클래스나 메소드 내부에서 사용되는 객체의 타입안정성 높임
* 반환값에 대한 타입 변환 및 타입 검사에 들어가는 노력을 줄임&#x20;

참고 : [functional-interface](../behavior-parameterization/functional-interface/ "mention")

</details>

{% embed url="https://docs.oracle.com/javase/tutorial/collections/interfaces/index.html" %}

{% embed url="https://hudi.blog/java-collection-framework-1/" %}
