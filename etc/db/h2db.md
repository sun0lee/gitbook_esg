---
description: 로컬환경에서 DB 사용하기
---

# H2DB 설치

## 1. 설치&#x20;

아래 링크에서 다운받고 압축 풀면 끝 .

* 다운로 링크 : [http://h2database.com/html/main.html](http://h2database.com/html/main.html)



## 2. 데이터베이스 실행 &#x20;

압축을 푼 디렉토리 내 bat 파일 또는 sh 파일 실행&#x20;

* &#x20;H2DB > h2 > bin > `h2.bat (h2.sh)`&#x20;
  * window는 `h2.bat` 더블 클릭&#x20;
  * mac은 terminal.app (까만창)에서 `./h2.sh`  입력하면 실행됨&#x20;



## 3. 초기설정&#x20;

*

    <figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>
* 저장한 설정 : <mark style="color:red;">`Generic H2 (Embeded)`</mark> -> 선택&#x20;
*   JDBC URL : <mark style="color:red;">`jdbc:h2:~/H2DB/db/ESG`</mark> -> 입력&#x20;

    * URL에 <mark style="color:red;">`ESG`</mark> 는 데이터베이스명, <mark style="color:red;">`H2DB/db`</mark> 는 데이터베이스 파일경로임 (사진 참조)



이렇게 입력 후 '연결' 클릭하면 아래처럼 경로에 ESG.mv.db 파일 생성됨.   &#x20;

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

## 4. 접속 (저장한 설정 및 <mark style="color:red;">JDBC URL 변경</mark>★★ )

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

* 저장한 설정 : <mark style="color:red;">`Generic H2 (Server)`</mark> -> 선택&#x20;
* JDBC URL : <mark style="color:red;">`jdbc:h2:tcp://localhost/~/H2DB/db/ESG`</mark> -> 입력

> 데이터베이스 파일을 생성한 다음에 JDBC URL을 저렇게 변경하는 이유는 파일 직접 접근이 아닌 TCP 소켓을 통해 접속해야 어플리케이션과 콘솔이 동시에 접근했을 때 오류가 발생하지 않기 때문이다.

{% embed url="https://atoz-develop.tistory.com/entry/H2-Database-%EC%84%A4%EC%B9%98-%EC%84%9C%EB%B2%84-%EC%8B%A4%ED%96%89-%EC%A0%91%EC%86%8D-%EB%B0%A9%EB%B2%95" %}

다 설치하고 마지막에 url을 변경하지 않아서 자꾸 오류가 발생함 ㅠㅠ 그래서 결국 이 문서를 작성함. 이해는 잘 안가지만 Database file을 직접 읽으냐 마냐의 차이인듯.&#x20;

&#x20;\-> 아무튼 이렇게 하면 오류 없이 사용가능 !!!&#x20;



* server vs. embeded 차이 무엇 ?



&#x20;
