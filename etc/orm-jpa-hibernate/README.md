# ORM, JPA, Hibernate

### ORM (Object Relational Mapping)

> object/relational mapping is _<mark style="color:blue;">the automated (and transparent) persistence of objects</mark>_ in a Java application to the tables in a relational database, _<mark style="color:blue;">using metadata that describes the mapping between the objects and the database.</mark>_ ORM, in essence, works by (reversibly) transforming data from one representation to another.

* 객체 / 관계 연결&#x20;
  * &#x20;자바의 객체를 관계형DB 세상에 자동(_<mark style="color:blue;">automated</mark>_)으로 그리고 투명(_<mark style="color:blue;">transparent</mark>_)하게 가져오는 기술 !!&#x20;
* 어떻게 ?
  * _<mark style="color:blue;">using metadata that describes the mapping between the objects and the database</mark>_
  * 즉 내가 이걸 이해하고 잘 사용하기 위해서는 두 개를 연결하는 "metadata"를 어떻게 써야하는지를 이해해야 함.  [orm-metadata.md](orm-metadata.md "mention") 참조
    * [ ] hibernate에서는 xml으로 매핑함.
    * [x] JPA jdk5.0 이후부터는 annotation을 이용해서 가능하게 함.
* _<mark style="color:blue;">persistence</mark>_ means..
  * 개별 객체가 애플리케이션 프로세스 범위를 벗어나서도 유지될 수 있게 하는 것&#x20;
  * 각 객체를 DB에 저장할 수 있고, 나중에 특정 시점에 다시 생성할 수 있음.&#x20;



### JPA (Java Persistent API)

* 자바 ORM 기술에 대한 API 표준 명세 (표준 설계서 같은..)
  * 자기가 직접 뭘 어떻게 하는게 아니라 뭐를 어떻게 하기로 약속 !!
  * ORM을 사용하기 위한 인터페이스를 모아둔 것 (만들거면 이건 지켜줘 같은 것임.)

### Hibernate&#x20;

* JPA를 실제로 구현한 것 중에 하나. : ORM 프레임워크
*   Java의 객체와 RDB에 테이블을 연결해서 객체에 대한 정보를 영원히 간직하고 싶은 염원 구현을 hibernate가 쉽게 할 수 있도록 도와주는 프레임워크.&#x20;

    * 객체를 DB에 저장할 때 sql을 직접 작성할 필요 없고, 자바 컬렉션에 저장하듯 처리하면 됨. ex. session.save(s)&#x20;





###
