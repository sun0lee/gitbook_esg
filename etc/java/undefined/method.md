# Method

자바에서 동작을 정의하는 method. function이랑 비슷한데 클래스에 귀속된다는 특징이 있음. 어디에서나 동작하는게 아니라 특정 클래스 안에서의 동작을 정의한 것임.  &#x20;



**Overloading 오버로딩**

* 매개변수가 다른 동일한 이름의 메소드를 중복해서 정의하는 것.&#x20;





다들 자주 쓰는 건데 무슨 의도로 쓰는 것인지 알 수 없는 메서드들에 대해 찾아본 기록 남겨두기.&#x20;

<details>

<summary>ToString() </summary>

객체의 정보를 리턴하는 매서드, 해당 Class에 toString을 override하지 않으면 Object의 toString 결과를 출력함.&#x20;

* example
  * examClass@15db9742&#x20;
  * getClass().getName() + @ + hashCode값을 16진수화 시킨 것
* 이것으로는 디버깅 불가함 -> 실질적인 값이 궁금함&#x20;
* 그래서 해당 Class를 기준으로 override함
* 디버깅이나 trace를 위한 의미있는 정보를 가진 필드를 오버라이드 할 필요 있음

참고출처  [https://codingdog.tistory.com/entry/java-toString-%EB%A9%94%EC%84%9C%EB%93%9C-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EC%A0%95%EB%B3%B4%EB%A5%BC-%EC%B6%9C%EB%A0%A5%ED%95%9C%EB%8B%A4](https://codingdog.tistory.com/entry/java-toString-%EB%A9%94%EC%84%9C%EB%93%9C-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EC%A0%95%EB%B3%B4%EB%A5%BC-%EC%B6%9C%EB%A0%A5%ED%95%9C%EB%8B%A4)

</details>

<details>

<summary>Getter, Setter</summary>

* 외부에서 필드로 직접적으로 접근하는 것을 막고, 메서드를 통해 필드에 접근하도록 함. ( 필드의 접근제어자는 private로 정의함 )
* 이름처럼 뭔가를 가져다주거나, 값을 설정하는 동작을 함. 접근, 설정 시  예외처리를 추가 할 수 있음 (직접 접근을 막고 중간에 레이어를 둔다는 식&#x20;

<!---->

* [https://velog.io/@cksdnr066/getter-%EC%99%80-setter-%EB%8A%94-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B1%B8%EA%B9%8C](https://velog.io/@cksdnr066/getter-%EC%99%80-setter-%EB%8A%94-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B1%B8%EA%B9%8C)

</details>

<details>

<summary>deep copy vs. shallow copy</summary>

* 주소를 복사할지 (shallow copy) 객체 자체를 복사(deep copy)해서 새로 만들지.?
* 주소를 복사하면 값을 바꾸는 경우 원본의 값이 바뀌어 버림&#x20;
* 복사해서 먼가 변경하고 싶은데 원본은 영향 받지 않게 하고 싶을때 deep copy 사용&#x20;

(참고) [https://jackjeong.tistory.com/100](https://jackjeong.tistory.com/100)

</details>

<details>

<summary>compareTo()</summary>

compareTo() 메서드의 반환값은 두 객체를 비교하여 결과를 정수로 반환.&#x20;

1. 0 : 두 객체가 같은 경우
2. 양수 : 인자로 전달된 객체보다 큰 경우
3. 음수 : 인자로 전달된 객체보다 작은 경우

```java
// irCurveSce가 EDetSce.SCE06보다 큰 경우 
irCurveSce.compareTo(EDetSce.SCE06) > 0
```

</details>

