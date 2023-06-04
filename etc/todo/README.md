# Todo

<table><thead><tr><th width="99.5"></th><th></th></tr></thead><tbody><tr><td>03/30</td><td><p>공통적인 금리 관리부분 인터페이스 만들기 </p><ol><li><p>작업간 공통적으로 사용하는 부분(테너별 금리를 담는 통) 파악</p><ul><li>IrCurveYtm</li><li>IrCurveSpot</li><li>IrCurveSpotWeek</li></ul></li><li>인터페이스 만들고</li><li>기존에 생성할때 각자의 타입으로 가져왔던 부분을 생성한 인터페이스로 대체하기  </li><li>이렇게 하면 기존에 메서드에서 필요한 매개변수를 각각 String 형태로 정의해서 받던 것을  새로 정의한 객체 자체를 넘겨서 필요한 매개변수를 뽑아서 쓸 수 있음</li></ol></td></tr><tr><td>04/04</td><td><p>공통적으로 쓰는 부분 찾기 </p><ul><li><p><del>Biz 구분</del> -> <code>EApplBizDv</code> 추가 (ENUM 개념을 알고 써야 할텐데.. 이렇게 하는게 맞나 테스트 차원에서 생성해봄 )</p><ol><li><p>하고 싶은건 enum 안에 biz구분별  paramSwMap을 가져오고 싶은데 job110과 연관성 땜에 어디부터 손봐야 할지 모르겠음 ㅠ </p><ul><li>paramSwMap 인스턴스가 생성된 다음에야 알수 있는 정보라서 ENUM에서 이것을 참조하고자 할때 nullponintex 를 생각하면 참조하지 않는 편이 나음. </li><li>그런데 작업 절차상 무조건 paramSwMap 인스턴스는 있을 수밖에 없긴함. </li></ul></li><li><p>280 dcnt Rate biz applBizDv는 그외 다른 테이블에서 사용하는 업무구분코드와 성격이 다름. <code>_</code><em><code>L, _A</code></em>가 붙어 있어서 필터링 구분자로 사용하는 컬럼과 속성이 다름.</p><ul><li>이 경우는 동일 컬럼을 다른 의미로 사용하는 것 자체가 잘못이므로  </li><li>280 테이블 구조를 바꾸는게 맞아보이긴 하는데 일단 현재버전에서 db도 구조를 바꾸면 답이 없을 것 같으므로 소스에서 어떻게든 처리. </li></ul></li></ol></li></ul></td></tr><tr><td>04/06</td><td><p>작업마다(paramSw의 정의한 단위마다) 혹은 금리마다 llp 를 다시 정의하여 가지고 있는 것이 의미가 있나 (목적에 따라 llp를 다르게 설정할 경우가 있을끼?) </p><ul><li>baseTenor</li><li>LLP </li></ul><p>기준일자 엔진실행시 파라메터로 받는 bssd</p><ul><li><p>매번 기준일자를 매개변수로 가지고 다녀야 하나.. </p><ul><li>사용하고자 하는 데이터가 범위기간일때는 필요함 </li></ul></li></ul></td></tr><tr><td>4/18</td><td><ul><li><p>github Page : hosting svc   <a href="https://docs.github.com/ko/pages">https://docs.github.com/ko/pages</a></p><ul><li>로컬 컴퓨터에서 웹호스팅을 할 경우에는 24시간 컴퓨터를 켜두어야 하고 웹서비스가 에러는 없는지 모니터링 해야 하는 등 관리 작업이 필요하다. 일반인들이 이러한 관리를 수행하기는 힘들기 때문에 로컬 컴퓨터를 통한 웹호스팅 보다는 GitHub Pages를 이용하는 것</li></ul></li><li><p>markdown 파일을 사용하는 블로그 플랫폼 <a href="https://jekyllrb-ko.github.io/">https://jekyllrb-ko.github.io/</a></p><ul><li>웹사이트를 운영하기 위한 html 관련 지식들을 알 필요가 없이, markdown 파일로 간단히 문서를 작성하면 html 파일로 이를 변환해주는 작업을 해주며 변환 결과물을 토대로 웹사이트를 구축해서 서비스</li></ul></li><li>테마 (css)  : minimal-mistakes</li><li>js ? jsp ??</li></ul></td></tr><tr><td></td><td></td></tr></tbody></table>

<details>

<summary></summary>

정적 웹사이트(static websites)의 대한 정의만 살펴보자. 정적 웹사이트는 어떤 웹사이트 주소를 접속한다면 모든 사람들이 모든 결과물(html)이 동일하면 정적인 웹사이트이다. 이미 html 페이지가 구성이 되어있어서 사람들이 해당 주소에 접속했을때 보여주는 결과물이 하나 뿐인 웹사이트이다.

동적으로 구성된 웹사이트들은 동일 웹사이트에 접속을 해도 사용자의 의한 요청에 따라 보여지는 웹페이지가 각기 다른 경우가 있다. 이런 경우 보통 웹어플리케이션 서버를 운영하고 사용자 요청에 따른 웹페이지 결과를 구성해서 보여주는 형태이다.

</details>
