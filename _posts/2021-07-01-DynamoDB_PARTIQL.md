---
title:  "DynamoDB PARTIQL"
excerpt: ""

categories:
  - DynamoDB
last_modified_at: 2020-07-01T08:06:00-05:00
---




함수




EXISTS 함수
EXISTS를 사용하여 ConditionCheck에서 TransactWriteItems API와 동일한 기능을 수행한다.

EXISTS 함수는 트랜잭션에서만 사용할 수 있다

값이 비어 있지 않은 컬렉션인 경우 값이 주어진 경우 결과로 TRUE를 반환, 그렇지 않은 경우 FALSE를 반환.



문법

EXISTS ( statement )


예시)

EXISTS(
    SELECT * FROM "Music" 
    WHERE "Artist" = 'Acme Band' AND "SongTitle" = 'PartiQL Rocks')




BEGINS_WITH 함수
지정된 속성이 특정 하위 문자열로 시작하는 경우 TRUE를 반환.



문법

BEGINS_WITH("속성", '문자열')


예시)

SELECT * FROM "Orders" WHERE "OrderID"=1 AND begins_with("Address", '7834 24th')




MISSING 함수
항목에 지정된 속성이 포함되지 않은 경우 TRUE를 반환.

같음 및 부등식 연산자만 이 함수와 함께 사용할 수 있다.



문법

"속성" IS | IS NOT MISSING


예시)

SELECT * FROM Music WHERE "Awards" is MISSING




ATTRIBUTE_TYPE 함수
지정된 경로의 속성이 특정 데이터 유형인 경우 TRUE를 반환



문법

attribute_type( "속성", '타입' )


예시)

SELECT * FROM "Music" WHERE attribute_type("Artist", 'S')




CONTAINS 함수
경로에서 지정한 속성이 다음 중 하나인 경우 TRUE를 반환.

특정 하위 문자열이 포함된 문자열.

집합 내의 특정 요소를 포함하는 집합.



문법

contains( "속성 or 문서 경로", '문자열 or 집합멤버' )


예시)

SELECT * FROM "Orders" WHERE "OrderID"=1 AND contains("Address", 'Kirkland')




SIZE 함수
속성의 크기를 바이트로 나타내는 숫자를 반환.



문법

size("속성 or 문서 경로")


예시)

SELECT * FROM "Orders" WHERE "OrderID"=1 AND size("Image") >300


select




문법

SELECT expression  [, ...] 
FROM table[.index]
[ WHERE condition ] [ [ORDER BY key  [DESC|ASC] , ...] 




예시)

// hk 가 DSHOP$ 으로 시작하고
// tr_no 가 있고
// dshop_no 가 있고
// sk 가 DSHOP 이고
// del_yn 이 N 인 데이터를
// dp_sshop 에서 가져오는 쿼리
SELECT * FROM "dp_sshop" where begins_with("hk", 'DSHOP$') and "tr_no" is not MISSING and "dshop_no" is not MISSING
and sk = 'DSHOP' and del_yn = 'N'




reference : https://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/developerguide/ql-reference.html



