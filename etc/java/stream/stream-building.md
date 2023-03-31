# Stream Building

연속된 데이터 목록만 있으면 스트림을 만들 수 있다 ?

## 1. 인스턴스를 이용해 스트림 만들기&#x20;

### 1.1. 배열

```java
// Arrays.stream 배열을 인수로 받는 정적 메서드 
int[] numbers = {1,2,3,4,5,6,7,8};
int sum = Arrays.stream(numbers).sum(); 
```

### 1.2. 컬렉션&#x20;

컬렉션 인터페이스에 추가된 디폴트 메서드stream을 이용해서 스트림을 생성할 수 있음 .

```java
public interface Collection<E> extends Iterable<E> {
default Stream<E> stream(){
 return StreamSupport.stream()
  }
  ...
}
```

```java
List<String> list = Array.asList("a","b","c");
Stream<String> stream = list.stream()
```

## 2. 메서드(함수)로 스트림 만들기&#x20;

### 2.1. generate() &#x20;

generate() 메서드를 이용하면 [supplier-less-than-t-greater-than.md](../behavior-parameterization/functional-interface/supplier-less-than-t-greater-than.md "mention")(;인자는 없고 리턴값만 있는 함수형 인터페이스)에  해당하는 람다로 값을 넣을 수 있음.

```java
public static<T> Stream<T> generate(Supplier<T> s) {
 ...
} 
```

{% code overflow="wrap" %}
```java
Stream<String> generatedStream = 
    Stream.generate(()-> "gen").limit(10) ; 
```
{% endcode %}

### 2.2. iterate()&#x20;

iterate()는 요청할 때마다 값을 주기 때문에 무한 스트림;unbounded stream을  생성할 수 있음&#x20;

```java
Stream.iterate(1, n->n*2) // 1은 초기값
    .limit(10) // 무한히 반복할수 있기 때문에 10개만.
    .forEach(System.out::println) ; //각각의 요소들을 출력해줘 
```

{% code overflow="wrap" %}
```java
//iterate 는 predicate를 지원 ! 
Stream.iterate(1, n-> n< 100, n->n*2) // 덕분에 iterate는 어디까지? 판단가능 
       .forEach(System.out::println) ;  
```
{% endcode %}

### 2.3. Builder()&#x20;

builder() 메서드로 스트림을 리턴하기&#x20;

```java
Stream<String> builderStream 
= Stream.<String>builder()
    .add("aa")
    .add("bb")
    .add("cc")
    .build();
```

## 3. 비어있는 스트림 만들기&#x20;

null이 될 수 있는 객체를 스트림으로 만들기&#x20;

{% code overflow="wrap" %}
```java
// Stream.ofNullable 이용해서 System.getProperty에 제공된 키에 대응하는 속성이 없을때 null을 확인하기 
Stream<String> favorateFoodStream
= Stream.ofNullable(System.getProperty("food")) ; 
```
{% endcode %}

{% code overflow="wrap" %}
```java
Stream<String> value 
= Stream.of("aa","bb","cc").flatMap(key-> Stream.ofNullable(System.getProperty(key)));
```
{% endcode %}

## 4. 값으로 직접 만들기

```java
// Stream.of 임의의 수를 인수로 받는 정적 메서드 이용해서 스트림 만들기
Stream<String> stream = Stream.of("aa","bb","cc","dd") ;
stream.map(String::toUpperCase).forEach(System.out::println); 
```

&#x20;

## 5. 파일로 스트림 만들기&#x20;
