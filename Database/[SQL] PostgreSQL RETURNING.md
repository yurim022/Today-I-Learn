Returning cluase
=================

PGSQL 에는 Insert, Udpate, Delete 쿼리 실행 후 실행한 쿼리의 결과를 알려는 **RETRUNING** cluase가 있다. 

RETRUNING * 은 SELECT * FROM table 과 같다. 즉 쿼리를 실행한 행 전체가 출력된다.

### create table
```
-- 회원
CREATE TABLE member (
    id VARCHAR(10) PRIMARY KEY,
    password VARCHAR(10)
);
-- 회원 상세
CREATE TABLE member_detail (
    id VARCHAR(10) PRIMARY KEY,
    name VARCHAR(10)
);
-- 회원 로그
CREATE TABLE member_log (
    id VARCHAR(10),
    description TEXT,
    created TIMESTAMP
);
-- 회원 백업
CREATE TABLE member_backup AS SELECT * FROM member;
-- 회원 테스트 데이터
INSERT INTO member VALUES
    ('backup-id0', '12345678'),
    ('backup-id1', '12345678'),
    ('backup-id2', '12345678');
```


### insert example

```
WITH rows AS (
    INSERT INTO member (id, password)
    VALUES ('taekyun', 'pass1234') RETURNING id
)
INSERT INTO member_detail (id, name)
    SELECT id, '김태균' AS name
    FROM rows;
```


```
WITH new_member AS (
    INSERT INTO member (id, password)
    VALUES ('taekyun2', 'pass1234') RETURNING *
), new_member_detail AS (
    INSERT INTO member_detail (id, name)
    VALUES ('taekyun2', '김태균2') RETURNING *
)
INSERT INTO member_log (id, description, created)
    SELECT new_member.id, new_member_detail.name||' 추가', NOW()
    FROM new_member, new_member_detail;
```


### update example

```
WITH updated AS (
    UPDATE member SET password = 'pAss@123' WHERE password = 'pass1234' RETURNING id
)
INSERT INTO member_log (id, description, created)
    SELECT updated.id, updated.id||' 수정', NOW()
    FROM updated;
```


### delete example

```
WITH deleted AS (
    DELETE FROM member WHERE password = '12345678' RETURNING *
)
INSERT INTO member_backup SELECT * FROM deleted;
```


참고링크 
https://blog.gaerae.com/2015/10/postgresql-insert-update-returning.html
