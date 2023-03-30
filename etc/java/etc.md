# etc

## Type

<details>

<summary>ENUM .class</summary>

**enumeration type**

참고 출처  [https://velog.io/@kyle/%EC%9E%90%EB%B0%94-Enum-%EA%B8%B0%EB%B3%B8-%EB%B0%8F-%ED%99%9C%EC%9A%A9](https://velog.io/@kyle/%EC%9E%90%EB%B0%94-Enum-%EA%B8%B0%EB%B3%B8-%EB%B0%8F-%ED%99%9C%EC%9A%A9)

관련 있는 상수들의 집합. 코드값처럼 서로 관련이 있고, 발생할 항목이 뻔한 대상을 목록화 해서 관리.

코드값이 1이라고 다 같은 1이 아님. => 각각의 의미에 맞게 구분하려고 씀.&#x20;

* 값 자체를 사용할 수도 있고 `value()`&#x20;
* 함수처럼 각 항목에 대응되는 값을 활용할 수도 있고 `valueOf()`&#x20;
* 인덱스를 활용할 수 있음 `ordinal()`



</details>



## Lib

<details>

<summary>Lombok</summary>

설치  [https://projectlombok.org](https://projectlombok.org/)

참고 출처  [https://mangkyu.tistory.com/78](https://mangkyu.tistory.com/78)

이걸 왜 쓰느냐  :&#x20;

* java에서 Data를 정의해서 사용할때 VO class 를 만들면 꼭 해야하는 일들이 생김 (Getter, Setter, 생성자 만들기, ToString 등등 -> 이클립스 등에서 코드를 자동으로 짜줄만큼 빈번하게 반복되어온 일.)&#x20;
* class내에 variable을 직접 호출하지 않고 필요 시 Getter를 이용해서 불러다 씀. 캡슐화라고 함.(구체적인 구현방식은 남이 신경 안써도 된다는 취지) &#x20;

<!---->

* 그럼 다시 lombok이 뭐냐면 :&#x20;
  * annotation 기반(@로 시작하는 주석같은거)으로 코드를 자동완성해주는 라이브러리. 즉 @Getter, @Setter 이런식으로만 쓰면 위의 귀찮은 작업을 자동으로 다 해줌.
  * 게다가 log4j2 처럼 로그 클래스를 자동 완성시켜준다 ?? -> 대충 로그를 찍기 쉽게 해준다는 뜻으로 이해함.&#x20;
  * 참고로 @Data 이거 쓰면 거의 모든게 그냥 다 되는데 그렇기 때문에 좀 무겁다고 함. (비추)&#x20;



</details>



## 헷갈리는 개념&#x20;

<details>

<summary>framework vs. API ?</summary>

#### framework

프레임워크는 프로젝트 개발에 도움이되는 클래스, 도구 및 관련 구성 요소 집합.

프레임워크는 완성된 제품이 아닌 완성된 제품을 만들기 위해 개발자를 도와주는 또는 기반이 되는 역할을 한다. 즉, 소프트웨어의 특정 문제를 해결하기 위해 상호 협력하는 클래스와 인터페이스의 집합.

프레임워크는 애플리케이션 프로그래밍 인터페이스(API)와 유사합니다. 기술적인 측면에서 프레임워크에는 API가 포함되어 있습니다. 프레임워크는 프로그래밍의 기반인 반면, API는 프레임워크가 지원하는 요소에 대한 액세스 권한을 제공합니다.

#### API

Java API는 기능을 캡슐화하는 구성 요소 집합에 대한 인터페이스.

API는 Application Programming **Interface**. Java의 API는 각각의 메소드, 필드 및 생성자가 있는 미리 작성된 패키지, 클래스 및 인터페이스의 모음입니다. 때로는 프로그래머가 내부 구현에 대한 관심없이 특정 기술을 사용해야하는 경우가 있습니다. API는 이러한 상황에서 유용합니다. 개발자는 API에서 미리 정의 된 작업을 사용하여 응용 프로그램을보다 쉽게 ​​만들 수 있습니다. Java에는 4500 개 이상의 API가 있습니다

Java API는 소프트웨어를 작성하기위한 서브 루틴 정의, 통신 프로토콜 및 도구 세트입니다. Java Framework는 일반적인 기능을 제공하는 소프트웨어를 추가 사용자 작성 코드로 선택적으로 변경하여 응용 프로그램 별 소프트웨어를 제공하는 추상화입니다.

</details>

{% embed url="https://blog.naver.com/taeny_kk/222546612224" %}
