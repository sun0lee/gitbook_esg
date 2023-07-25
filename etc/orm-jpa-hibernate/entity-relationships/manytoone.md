# 단방향 ManyToOne

1. &#x20;IR\_CURVE (Master)
   * 금리커브를 정의하고 관리.
   * 이 예시에서는 어떤 테이블이 자신의 정보를 참조하는지 궁금하지 않음. (단방향을 설명할 것이므로)  &#x20;
2. IR\_CURVE\_YTM\_USR (세부정보)&#x20;
   * 기준일자별, 금리커브별 YTM을 관리
   * IR\_CURVE 와의 관계를 IR\_CURVE\_SID 라는 fk로 알고 있음 -> owner&#x20;



* IR\_CURVE\_YTM\_USR 은 IR\_CURVE\_SID라는 컬럼을 FK로 하여 IR\_CURVE의 정보를 참조하고 있음.

![](<../../../.gitbook/assets/image (5) (1).png>)

<details>

<summary>IrCurve</summary>

*

    <figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
*

    <figure><img src="../../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>
* IrCurve 정보를 읽을 때, IrCurveYtmUsr 등 자신을 참조하는 정보도 함께 알고 싶음 ? (x)
  * 이 경우 IrCurve class에는 관계를 정의하지 않음.&#x20;
* 만약 IrCurve 정보를 읽을 때, IrCurveYtmUsr 기준일자별 이력내역 정보도 함께 알고 싶다면 ( 단방향이 아니라면 )
  * oneToMany 관계를 class에 정의해야 함 => 다른 예시 page에서 설명.&#x20;

</details>

<details>

<summary>IrCurveYtmUsr</summary>

*

    <figure><img src="../../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>
*

    <figure><img src="../../../.gitbook/assets/image (37) (1).png" alt=""><figcaption></figcaption></figure>
* IrCurveYtmUsr가 relation owner 임 (관계의 주인을 Owner라고 함)

```java
@ManyToOne
@JoinColumn(name = "IR_CURVE_SID" , referencedColumnName ="SID")
private IrCurve irCurve ;
```

* IrCurveYtmUsr를 읽을 때 관련된 IrCurve 정보도 알고 싶음. (o) ->필요한 쪽이 관계의 주인이 됨&#x20;
  * IrCurveYtmUsr (Many)  <- IrCurve (one)
  * IR\_CURVE\_YTM\_USR.IR\_CURVE\_SID = IR\_CURVE.SID &#x20;

</details>

