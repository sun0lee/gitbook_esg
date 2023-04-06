# Constructor

1. 클래스의 인스턴스를 생성할 때 자동으로 호출되는 메서드.&#x20;
2. 클래스에 선언하는 메서드 중 하나. 근데 일반적인 메서드랑 다른 점이 있음.
   * 인스턴스 생성시 자동 호출된다. 멤버 변수 (필드)나 상수를 초기화하는 역할을 함. -> 명령어 new 에 따라 자동으로 call
   * 반환값이 없다. void&#x20;
   * 클래스의 이름과 같다. -> 메서드 이름은 통상 소문자로 시작하는데, 생성자는 클래스명과 같이 대문자로 시작함.  &#x20;
3. 자바 클래스는 반드시 하나 이상의 생성자가 있어야 함. (만약에 정의 안하면 )

* **Default constructor ; 디폴트 생성자**
  * 생성자가 없는 클래스는 클래스 파일을 컴파일 할 때 자바 컴파일러에서 자동으로 생성자를 만들어 줌.
  * 매개변수가 없고 구현코드도 없음.

4. 생성자가 여러 개다 ??

* &#x20;**overload ; 오버로드**&#x20;
  * 객체 지향 프로그램에서 매서드 이름이 같고 매개변수만 다른 경우&#x20;
  * 클래스에 생성자가 두 개 이상 제공되는 경우 생성자 오버로드라고 함.&#x20;
  * 필요에 따라 매개변수가 다른 생성자를 여러 개 만들 수 있음.&#x20;
  * 이 클래스를 사용하는 코드에서 원하는 생성자를 선택하여 사용할 수 있음.
* this() ; 생성자 호출문&#x20;
  * 생성자에서만 사용할 수 있음. 생성자에서 다른 생성자를 호출할 때 사용.&#x20;
  * 명령문 중 첫 번째 명령문으로만 올 수 있음 !!!!&#x20;

<details>

<summary>super, super()</summary>

#### super : 참조변수&#x20;

부모 클래스로부터 상속받은 필드나 메소드를 자식 클래스에서 참조하는데 사용하는 참조 변수 .

자식 클래스는 부모클래스를 상속받았기 때문에 부모의 properties를 사용할 수 있음. 부모 클래스와 자식 클래스의 멤버 이름이 같을 경우 super 키워드를 사용하여 구분.&#x20;

ref. 인스턴스 변수의 이름과 지역변수의 이름이 같을 경우 인스턴스 변수 앞에 this 키워드를 사용함.&#x20;

#### super() : method

자식클래스가 인스턴스를 생성하면, 인스턴스 안에는 자식 클래스의 고유 멤버변수와 부모변수의 멤버 변수가 포함되어 있음.&#x20;

상속에서의 생성자는 상속되지 않은 유일한 멤버 함수임. 즉, 부모클래스의 멤버를 초기화 하기 위해서는 부모클래스의 생성자도 호출해야 함. => 자식 클래스 생성자를 호출할 때 부모 클래스 생성자를 먼저 호출해야 함.&#x20;

* 매개변수가 없는 생성자의 경우 자바 컴파일러가 자동으로 super() 메소드를 추가해줌.&#x20;
* 매개변수가 있는 (부모 클래스의 생성자가 오버로딩된 경우) 자바 컴파일러가 자식 클래스 생성자 호출 시 super() 를 추가해주지 않음 !!!  => 명시적으로 추가하기&#x20;

(출처) [http://www.tcpschool.com/java/java\_inheritance\_super](http://www.tcpschool.com/java/java\_inheritance\_super)

</details>

<details>

<summary>Example Code </summary>

* SmithWilsonKics class 는 IrModel을 상속받음&#x20;
  * 자식 클래스 : SmithWilsonKics
  * 부모 클래스 :  IrModel
  *

      <figure><img src="../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>
* SmithWilsonKics에 정의된 매개변수가 있는 생성자&#x20;
  * SmithWilsonKics(baseDate, ... )
  *

      <figure><img src="../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>
* super() -> IrModel()&#x20;
  * 초기화 시점에 IrModel의 멤버변수도 초기화 하기&#x20;

</details>



<details>

<summary>Lombok.@Builder</summary>

* 생성자에 매개변수가 많을 때. builder class를 만들면 유용함.
  * 매핑관계를 한눈에 확인할 수 있기 떄문임.&#x20;
* Lombok의 @Builder는 간단히 어노테이션으로 builder class를 작성하지 않아도 bulider를 사용할 수 있게 해줌&#x20;

[https://velog.io/@becolorful/Lombok-Builder-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC](https://velog.io/@becolorful/Lombok-Builder-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC)

{% code title="Esg150_YtmToSpotSw.class" %}
```java
SmithWilsonKicsBts swBts = SmithWilsonKicsBts.of()
               .baseDate(DateUtil.convertFrom(baseYmd))
               .ytmCurveHisList(ytmRst)
               .alphaApplied(alphaApplied)			 
               .freq(freq)
               .build();
```
{% endcode %}

{% code title="SmithWilsonKicsBts.class" %}
```java
@Builder(builderClassName="of", builderMethodName="of")
public SmithWilsonKicsBts(LocalDate baseDate, List<IrCurveYtm> ytmCurveHisList, Double alphaApplied, Boolean isRealNumber, Integer freq, Double liqPrem) {				
  super();		
  this.baseDate = baseDate;		
  this.setTermStructureYtm(ytmCurveHisList);
  this.setLastLiquidPoint(this.tenor[this.tenor.length-1]);
  this.isRealNumber = (isRealNumber == null ? true : isRealNumber);		
  this.alphaApplied = (alphaApplied == null ? 0.1  : alphaApplied);		
  this.freq         = (freq         == null ? 2    : freq        );
  this.liqPrem      = (liqPrem      == null ? 0.0  : liqPrem     );
  
  double toRealScale = this.isRealNumber ? 1 : 0.01;
  for(int i=0; i<this.iRateBase.length; i++) this.iRateBase[i] = toRealScale * this.iRateBase[i];		
      
  this.ltfr = this.iRateBase[this.iRateBase.length-1];
  this.ltfrT = (int) this.lastLiquidPoint;
  this.ltfrCont = irDiscToCont(this.ltfr);
  this.liqPrem = toRealScale * this.liqPrem;
}
```
{% endcode %}

</details>
