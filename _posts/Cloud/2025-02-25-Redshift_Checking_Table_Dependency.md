---
title:  "[Redshift] DROP TABLE 시 의존성 확인"
date:   2025-02-25 00:00:00 +0900
categories:
  - AWS
tags:
  - Cloud
  - AWS
  - Redshift
toc: true
toc_sticky: true
toc_label: "의존성 확인"
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/6e5c4a52-08fd-4048-ace8-18cba336c4e7
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
AWS Redshift에서 DROP TABLE을 사용할 시에 의존성을 사전에 체크하는 여러 방법에 대해 기록해놓습니다.
{: .notice--info}

<br>

![redshift](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/6e5c4a52-08fd-4048-ace8-18cba336c4e7){: .align-center}{: width="70%" height="70%"} 

<br><br>

## DROP TABLE 시 에러 발생
![Image](https://github.com/user-attachments/assets/8892a765-4612-43bb-89ac-6470b3db8640)
  
&nbsp;`DROP TABLE` 명령어로 테이블을 제거하고자 할 때 위와 같은 에러를 발견하실 수 있습니다. 이 에러 내용은 해당 테이블에 Dependency, 즉 의존성이 있기 때문에 삭제할 수 없다는 문제입니다.  
&nbsp;그 아래 `DROP ... CASCADE`를 사용해서 강제로 삭제하라고 하는데 이 CASCADE 명령어를 사용하면 의존성 걸려있는 것들도 같이 삭제되기 때문에 신중해야 합니다. 잘못하면 해당 테이블을 바라보고 있는 유저가 사용 중인 `View`도 삭제될 수 있기 때문이죠. 그렇기 때문에 하나씩 케이스를 찾아봅니다.

<br><br>

## View 확인
&nbsp;첫번째로 확인할 부분은 View입니다. 해당 테이블을 이용해서 View를 만들어 제공하는 경우가 가장 많은 케이스로 보입니다. 이를 사전에 확인하는 쿼리는 다음과 같습니다.  
  
```sql
SELECT * 
  FROM information_schema.view_table_usage 
 WHERE table_schema='[스키마 명]' 
   AND table_name = '[테이블 명]'
;


SELECT * 
  FROM pg_views
 WHERE definition like '%[테이블명]%'
;

-- View가 필요 없다면
DROP VIEW [스키마 명].[View 명];

-- 의존성에 걸리는 테이블을 변경할 경우 
-- Normal View를 Late Binding View로 변경해야할 경우에는 DROP VIEW를 먼저 진행해야 합니다.
CREATE OR REPLACE VIEW [스키마 명].[View 명] AS
~~
;
```

&nbsp;`view_table_usage`는 데이터베이스(catalog), 스키마, View/Table 명의 정보들로 구성되어 직관적으로 확인할 수 있다는 장점이 있습니다. 하지만 이 `view_table_usage`도 Postgres 테이블들로 구성된 View이기 때문에 없는 데이터가 있을 수도 있습니다.  
&nbsp;그렇기에 저는 `pg_views`와 같이 확인하고 있습니다. `pg_views`에는 View에 대한 스키마 명, View 명, Owner 그리고 DDL 쿼리로 구성되어 있습니다. 그 DDL 쿼리에서 해당 테이블이 있는지 확인하는 방식으로 View를 찾고 있습니다.

<br><br>

## CONSTRAINT 확인
&nbsp;다음으로는 `제약조건 Constraint`이 걸리는 상황입니다. Redshift에서 PK를 선호하지는 않지만 PK가 걸려있거나 FK가 걸려있는 경우를 찾기 위함입니다.

```sql
-- PK 확인
SELECT *
  FROM pg_constraint
 WHERE conrelid = '[스키마 명].[테이블 명]'::regclass
;

-- FK 확인
SELECT *
  FROM pg_constraint
 WHERE confrelid = '[스키마 명].[테이블 명]'::regclass
;

-- 제약조건 삭제
-- 여기서 "Constraint 명"은 위 쿼리 결과에서 "conname" 컬럼을 말함
ALTER TABLE [스키마 명].[테이블 명] DROP CONSTRAINT [Constraint 명];
```
  
&nbsp;제약 조건이 걸려있다면 이것 또한 테이블을 DROP할 수 없게 만드는 요인인데 PK인지 FK인지 확인해야 합니다. PK의 경우 무난하게 제약 조건을 삭제 후 테이블을 Drop할 수 있겠지만 FK의 경우 다른 테이블의 확인도 필요하겠죠.

<br><br>

## STORED PROCEDURE 또는 FUNCTION 확인
&nbsp;세번째로는 `Procedure` 또는 `Function`을 확인하는 것입니다. 해당 Procedure 또는 Function 내부에서 테이블을 사용하고 있다면 이것도 문제가 되겠죠.
  
```sql
SELECT *
  FROM pg_proc
 WHERE prosrc ILIKE '%[테이블명]%'
;

-- 삭제
DROP PROCEDURE [Procedure 명];
DROP FUNCTION [Function 명];
```
  
&nbsp;문제가 되는 Procedure나 Function을 찾았다면 삭제하거나 내부 로직에서 테이블을 변경해서 다시 재정의하는 작업이 필요합니다.

<br><br>

## 그외
&nbsp;위에서 봤던 View를 검사할 때 안나왔던 Materialized View를 체크해야할 수도 있습니다. 이럴 때는 아래 쿼리를 이용해서 검사해봅니다.

```sql
SELECT DISTINCT 
       c.relname
     , n.nspname
     , c.relkind
  FROM pg_class c
  JOIN pg_depend d
    ON c.oid = d.objid
  LEFT JOIN pg_namespace n 
    ON c.relnamespace = n.oid
 WHERE d.refobjid = '[스키마 명].[테이블 명]'::regclass
;
```
&nbsp;위의 쿼리를 실행해서 결과가 나오면 확인합니다. 여기에서 `relname`은 클래스 명, `nspname`은 스키마 명, `relkind`는 다음과 같습니다.
* r : 일반 테이블
* v : View
* m : Materialized View
* i : Index

&nbsp;위의 결과 값을 보고 해당되는 것이 나왔다면 케이스에 맞게  처리하도록 합니다.

<br><br>

## 정리
&nbsp;DROP TABLE 명령어로 테이블을 삭제할 때 다음과 같이 의존성이 있는지 확인을 하고 문제가 없다면 처리하는 것이 좋습니다. `CASCADE`는 편리하지만 불상사를 불러올 수 있기 때문에 신중하게 사용해야겠습니다.