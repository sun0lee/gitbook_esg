# Mapping persistent classes

## 1. Entity type 객체 vs Value type 객체&#x20;

* 테이블보다 클래스가 더 많은 설계 (한 행을 다수의 인스턴스로 표현하는 설계)
* 모든 것을 Value 타입 클래스로 만들고 절대적으로 필요한 경우에만 Entity로 승격해야 함.&#x20;
* 연관관계는 최대한 단순하게 !!

| Entity type 객체                                                                                                                                              | Value type 객체                                                                                                                                                                                                   |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>시스템에서 구성단위가 더 큰 클래스. </p><ul><li>인스턴스에는 <strong>독립적인 생명주기</strong>와 <strong>자체적인 식별자(id)가 있음</strong>. </li><li>여러 다른 인스턴스에서 참조(공유참조) 할 수 있음. </li></ul> | <p>Value 타입은 <strong>특정 엔티티 클래스에 의존적</strong>임. </p><ul><li>Value 타입 인스턴스의 생명주기는 자신을 소유하는 Entity 인스턴스의 생명주기에 따라 달려있음.</li><li> <strong>오직 한 Entity에서 참조</strong>될 수 있음. (공유참조 불가)</li><li>식별자 (id) 없음</li></ul> |
| <p>자신만의 데이터베이스 동일성(pk)을 갖음</p><p>Entity 인스턴스에 대한 객체 참조는 데이터베이스에 참조(fk)로 저장됨. </p>                                                                           | <p>데이터베이스 동일성을 갖지 않음</p><p>식별자(id)나 식별자 프로퍼티가 없음. </p>                                                                                                                                                          |
|                                                                                                                                                             | <ul><li>Component : Entity와 동일한 테이블에 저장되는 사용자 정의 클래스. </li><li>연관관계는 단방향 모델링 (Entity type 객체 -> Value type 객체 ) </li></ul>                                                                                      |

## 2. 식별자(id)가 있는 Entity 매핑&#x20;

* &#x20;java 동일성 vs. java 동등성 vs. DB 동일성

| java 동일성 (identity)                                                                                                     | java 동등성 (equality)                                                                                                                                                                                                                   | RDB 동일성 (identity)                                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>== </p><p>두개의 객체가 같은 메모리 위치를 가리키고 있다 </p>                                                                            | <p>equal() </p><p>메서드 구현하는 클래스에 의해 정의, 서로 동일하지 않은 객체가 같은 값을 갖는 것. </p><p>메모리 공간에서 점유하는 위치가 각자 다르더라도 디른 두 String이 같은 문자배열을 나타낸다면 같다고 봄 </p>                                                                                            | 같은 행을 나타내거나, 동등하게 같은 테이블과 주 키 (pk)값을 공유할 때 동일하다.                                                                                                   |
| Objects are identical if they occupy the same memory location in the JVM. This can be checked by using the == operator. | Objects are equal if they have the same value, as defined by the equals (Object o) method. Classes that don’t explicitly override this method inherit the implementation defined by java.lang.Object, which compares object identity. | Objects stored in a relational database are identical if they represent the same row or, equivalently, share the same table and primary key value. |
|                                                                                                                         |                                                                                                                                                                                                                                       | <p>잘 만든 pk 조건 </p><ul><li>유일하고</li><li>변하지 않으</li><li>비어있지 않음 </li></ul>                                                                           |

* 하이버네이트에서 동일성 확인
  * persistent instance 의 the identifier property 값
  * Session.getIdentifier(Object o) 리턴 값&#x20;

## 3. 클래스 매핑 옵션&#x20;

* 불변성을 띄는 Entity : update가 될 필요가 없는 Entity (-> 마스터성 input 정보들 ??)
* Entity 이름 부여
* 패키지 이름 선언
* sql 식별자 인용부호 예약어 escape &#x20;
* prefix&#x20;

<details>

<summary>NamingStrategy</summary>

{% code title="hibernate.cfg.xml" %}
```xml
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD//EN"
    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
  <session-factory>
    <property name="hibernate.physical_naming_strategy">com.gof.util.PhysicalNamingStrategyImpl</property>    
```
{% endcode %}

{% code title="PhysicalNamingStrategyImpl.java" %}
```java
public class PhysicalNamingStrategyImpl extends PhysicalNamingStrategyStandardImpl implements Serializable {	
	
	private static final long serialVersionUID = -3189474054653840418L;	
	public static final PhysicalNamingStrategyImpl INSTANCE = new PhysicalNamingStrategyImpl();
	
//	private static String prefix = EsgConstant.TABLE_PREFIX == null ? "E" : EsgConstant.TABLE_PREFIX;
	private static String schema = EsgConstant.TABLE_SCHEMA == null ? "PUBLIC" : EsgConstant.TABLE_SCHEMA;

	 
    @Override
	public Identifier toPhysicalSchemaName(Identifier name, JdbcEnvironment context) {

    	if(super.toPhysicalSchemaName(name, context)==null) {
    		return new Identifier(schema, false);
    	}
		return super.toPhysicalSchemaName(name, context);
	}
    
	@Override
	public Identifier toPhysicalTableName(Identifier name, JdbcEnvironment context) {
	    return new Identifier(addUnderscores(name.getText()), name.isQuoted());
	}

	@Override
	public Identifier toPhysicalColumnName(Identifier name, JdbcEnvironment context) {
	    return new Identifier(addUnderscores(name.getText()), name.isQuoted());
	}

	public static String addUnderscores(String name) {
				
	    final StringBuilder buf = new StringBuilder( name.replace('.', '_') );
	    
	    for (int i=1; i<buf.length()-1; i++) {	    
	        if (
	             Character.isLowerCase( buf.charAt(i-1) ) &&
	             Character.isUpperCase( buf.charAt(i) ) &&
	             Character.isLowerCase( buf.charAt(i+1) )
	         ) {
	             buf.insert(i++, '_');
	         }
	     }
	     return buf.toString().toUpperCase(Locale.ROOT);
	}
}
```
{% endcode %}

</details>

## 4. 구성단위가 세밀한 모델과 매핑 (Value 타입)

* 기본 프로퍼티 매핑&#x20;
* 포함클래스 (Component, embedded class) 매핑&#x20;
  * db 모델링에서는 동일한 테이블의 컬럼으로 구성되더라도 클래스 단위에서는 Entity 타입과 Value 타입으로 구분하여 세부 정보를 별도의 클래스로 세분화된 모델링이 가능함.
  * 즉 db와 자바의 class설계가 반드시 1:1일 필요는 없음.&#x20;
  * class 설계 ( Entity type : Value type = User : Address )
  *

      <figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>
  * DB 모델 설계 (USER 테이블 내에 세부정보 Address가 컬럼으로 정의되어 있음)&#x20;
  * ![](<../../.gitbook/assets/image (14).png>)
