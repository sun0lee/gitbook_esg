# Inheritance (상속)

부모 paraent /. super class <-> 자식 child / sub class

is a 관계로 정의되는 경우.&#x20;

단일 상속 : 자식은 부모를 1개만 가질 수 있음.

객체 생성시 부모 클래스의 내용이 자동으로 포함됨&#x20;

<details>

<summary>super 참조변수 </summary>

부모 클래스로부터 상속받은 필드나 메소드를 자식 클래스에서 참조하는데 사용하는 참조 변수 .

자식 클래스는 부모클래스를 상속받았기 때문에 부모의 properties를 사용할 수 있음. 부모 클래스와 자식 클래스의 멤버 이름이 같을 경우 super 키워드를 사용하여 구분.&#x20;

ref. 인스턴스 변수의 이름과 지역변수의 이름이 같을 경우 인스턴스 변수 앞에 this 키워드를 사용함.&#x20;

(출처) [http://www.tcpschool.com/java/java\_inheritance\_super](http://www.tcpschool.com/java/java\_inheritance\_super)

</details>



### `super.메서드명(인자);` 부모 메서드 호출

<-> this는 나 (자식)

super는 부모를 지칭함.



### `super()` ; 부모 생성자 호출&#x20;

자식클래스가 인스턴스를 생성하면, 인스턴스 안에는 자식 클래스의 고유 멤버변수와 부모변수의 멤버 변수가 포함되어 있음.&#x20;

상속에서의 생성자는 상속되지 않은 유일한 멤버 함수임. 즉, 부모클래스의 멤버를 초기화 하기 위해서는 부모클래스의 생성자도 호출해야 함. => 자식 클래스 생성자를 호출할 때 부모 클래스 생성자를 먼저 호출해야 함.&#x20;

* 매개변수가 없는 생성자의 경우 자바 컴파일러가 자동으로 super() 메서드를 추가해줌.&#x20;
* 매개변수가 있는 (부모 클래스의 생성자가 오버로딩된 경우) 자바 컴파일러가 자식 클래스 생성자 호출 시 부모의 생성자 ; super() 를 추가해주지 않음 !!!  => 명시적으로 추가하기&#x20;

<details>

<summary>Example Code </summary>

* SmithWilsonKics class 는 IrModel을 상속받음&#x20;
  * 자식 클래스 : SmithWilsonKics
  * 부모 클래스 :  IrModel
  *

      <figure><img src="../../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>
* SmithWilsonKics에 정의된 매개변수가 있는 생성자&#x20;
  * SmithWilsonKics(baseDate, ... )
  *

      <figure><img src="../../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>
* super() -> IrModel()&#x20;
  * 초기화 시점에 IrModel의 멤버변수도 초기화 하기&#x20;

</details>



### **Overriding 오버라이딩**&#x20;

* 상속받은 메서드의 내용을 재정의하는 것.&#x20;
  * 상속한 메서드의 본문만 변경가능
  * 상속한 메서드의 선언부 변경불가
  * 접근제한자는 부모의 메서드와 같거나 넓은 범위로만 변경 가능
  * 메서드 호출 우선순위는 오버라이딩한 메서드가 부모보다 우선 적용함.&#x20;
