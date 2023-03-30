---
description: ORM에서 objects와 database를 매핑을 정의하는 metadata에 대해 살펴봄
---

# ORM metadata

### metadata 정의 :&#x20;

* 일반적으로 데이터에 관한 구조화된 데이터
* 대량의 정보 가운데에서 확인하고자 하는 정보를 효율적으로 검색하기 위해 원시데이터(Raw data)를 일정한 규칙에 따라 구조화 혹은 표준화한 정보를 의미
* 데이터의 데이터라고 해서 말장난 같은데 (데이터 자체를 관리하기 위한) 데이터 라고 해석&#x20;
  * 실제로 연산에 필요한 원천 데이터는 테이블 혹은 Entity에 있는 것. (우리가 일반적으로 알고 있는 것)&#x20;
  * 메타데이터는 (엔티티랑 table의 매핑 관계)에 관한 정보, 그것도 데이터니까.

ORM에서 metadata를 관리하는 방식은 hibernate (구현체) 와 JPA (설계서) 랑 조금 상이함. 시간에 순서에 따라 계속 바뀌어오고 있는 결과임.&#x20;

### hibernate core&#x20;

* xml을 통해 정의하도록 되어 있음 (아래 예시 참조)
* DB <-> Object
* table <-> entity
* column <-> field&#x20;

<details>

<summary>Hibernate -> use XML</summary>

* &#x20;Entity class&#x20;
*

    <figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

xml : Entity class <-> table 매핑&#x20;

*

    <figure><img src="../../.gitbook/assets/image (87).png" alt="11"><figcaption></figcaption></figure>

</details>

{% embed url="https://docs.jboss.org/hibernate/core/3.3/reference/en/html/tutorial.html" %}

### JPA&#x20;

* jdk5.0 부터 annotation으로 entity class 정의할 때 주석처럼 매핑정보 입력함.&#x20;
* `import javax.persistence.* ;`

<details>

<summary>Java Persistence API -> use Annotation </summary>

* class
*

    <figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

</details>

{% embed url="https://www.oracle.com/technical-resources/articles/javase/persistenceapi.html" %}

{% embed url="https://docs.oracle.com/javaee/7/tutorial/persistence-intro001.htm#BNBQA" %}
