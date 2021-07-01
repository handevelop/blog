---
title:  "DynamoDB DQL"
excerpt: ""

categories:
  - DynamoDB
last_modified_at: 2020-07-01T08:06:00-05:00
---


# DQL 이란 

- Python 기반 + CLI 사용
(https://github.com/stevearc/dql)


# DQL 특징

- DCL/DML 지원
- INSERT/UPDATE/DELETE Bulk 지원

# DQL  사용해보기

1. dql 설치

```java
pip install dql
```


2. AWS credential 설정

```java
cd .aws
handongjin:.aws handongjin$ pwd
/Users/handongjin/.aws
--> .aws 폴더에서 credentials 수정해주기
 
vi credentials
[default]
// 여기에 dql 로 사용할 크리덴셜 정보 입력
```


3. dql 리전 변경하여 접속하기 (default 값 : us-west-1)

```java
dql -r ap-northeast-2  // 아시아 태평양(서울)리전으로 변경하며 dql 실행
```


4. 쿼리 사용하여 조회 해보기 


```java
ap-northeast-2> select * from ddb_table where hk = 'MSG$CS+KR';
--------------------------------------------------------------------------------
        reg_no  : '______'
     reg_st_cd  : 'BO_CC'
       mod_time : '2020-12-31 18:48:16'
             hk : 'MSG$CS+KR'
       msg_cnts : '올바른 값을 입력하세요.'
       reg_time : '2020-02-26 18:24:14'
             sk : 'MSG$CS+105'
Press return for next 1 result (or type 'all'):
```


# 운영 상황에서 언제 사용 할까?

- 운영 DDB 데이터에 새로운 Not Null 컬럼(우선순위)가 추가되었다 → 모든 데이터에 새로운 컬럼에 대해 기본값 100으로 Update 쳐줘야 하는 이슈 발생.



# DQL 사용하여 DDB bulk update 하기

- 흔히 사용하는 sql update 문을 사용하여 간단하게 update 가 가능하다

```java
ap-northeast-2> update ddb_table set 우선순위 = 100, mod_time = '2020-09-24 15:44:00' where sk = 'sk';
Updated 53 items
```


ref

1. https://www.slideshare.net/IanYoon/sql-aws-dynamodb-cli

2. https://dql.readthedocs.io/en/latest/