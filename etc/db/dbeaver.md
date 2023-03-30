---
description: 콘솔창에서 쿼리 및 db작업은 가능하나 사용하기에 넘나 불편함. 그래서 Dbeaver 연동하기
---

# Dbeaver 연결

## 1. Create Connection&#x20;

Database Navigator -> 마우스 우클릭 -> Create -> Connection

*

    <figure><img src="../../.gitbook/assets/스크린샷 2023-03-15 오후 1.56.21.png" alt=""><figcaption></figcaption></figure>

## 2. H2 DB 선택&#x20;

All -> `H2 Embeded` 선택 후 "다음"&#x20;

*

    <figure><img src="../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

## 3. Connection 정보 입력&#x20;

Connect by : URL

* JDBC URL :  <mark style="color:red;">`jdbc:h2:tcp://localhost/~/H2DB/db/ESG`</mark>
  * [#4.-jdbc-url](h2db.md#4.-jdbc-url "mention")의 url을 입력한다.&#x20;
*

    <figure><img src="../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

## 4. Driver 설정에서  H2 Librariy 추가&#x20;

`Driver Settings` 에서 `Libraries` 확인&#x20;

* 아래처럼 `h2.jar`가 있으면 상관없고 없으면 `Add File` 에서 찾아서 추가하기 .
*

    <figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>
* H2 db 압축 풀었던 위치에서 `h2-2.1.214.jar`를 선택 한다.&#x20;
  * `H2DB/h2/bin/h2-2.1.214.jar`
*

    <figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

## 5.  Dbeaver 사용하기&#x20;

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>
