---
title:  "[Redshift] DataShare"
date:   2023-11-30 00:00:00 +0900
categories:
  - AWS
tags:
  - Cloud
  - AWS
  - Redshift
toc: true
toc_sticky: true
toc_label: "Redshift DataShare"
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/6e5c4a52-08fd-4048-ace8-18cba336c4e7
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
AWS Redshift의 여러 기능 중에 DataShare에 대해 살펴봅니다. <a href="https://docs.aws.amazon.com/ko_kr/redshift/latest/dg/datashare-overview.html">공식 문서</a>를 참고하여 정리합니다.
{: .notice--info}

<br>

![redshift](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/6e5c4a52-08fd-4048-ace8-18cba336c4e7){: .align-center}{: width="70%" height="70%"} 

<br><br>

## DataShare 기능이란

&nbsp;Redshift는 `DataShare`라는 "데이터 공유" 기능을 제공합니다. 이는 여러 Redshift 클러스터끼리 데이터를 자동으로 공유하도록 하는 기능인데 A라는 클러스터에 있는 테이블에 데이터가 신규로 INSERT되었다면 B라는 클러스터에 있는 같은 테이블에도 해당 데이터가 실시간으로 동기화되는 기능입니다. Datashare에는 Producer와 Consumer의 개념이 나오는데 `Producer` 클러스터는 Datashare를 생성하고 객체를 추가하여 OUTBOUND로 다른 클러스터와 데이터를 공유하는 클러스터를 말하고 반대로 `Consumer` 클러스터는 INBOUND로 다른 클러스터로부터 데이터를 공유받는 클러스터를 말합니다..   

&nbsp;이러한 DataShare에 이용할 객체로는 데이터베이스, 스키마, 테이블, 뷰 및 UDF (User Defined Function, 사용자 정의 함수)가 포함됩니다. 또한, Access Control이 가능하여 세부적으로 조정할 수 있습니다.

<br>

### 고려 사항 및 제한 사항

* 동일한 리전 내에서의 DataShare는 추가 비용이 없지만 리전 간 DataShare는 추가 요금이 부가됨.
* Producer의 데이터를 쿼리하는 데 필요한 자원들은 Consumer가 부담합니다.
* Producer 클러스터와 Consumer 클러스터 모두 암호화해야  합니다.
* Python 및 Lambda UDF는 지원하지 않습니다.
* Consumer는 DataShare 객체를 다른 DataShare에 추가할 수 없습니다.

<br><br>

## DataShare 이용하기

### DataShare 생성 및 권한 관리 

&nbsp;Producer 클러스터는 Datashare에 대한 제어 권한을 가지고 있습니다. 이에 따라 Datashare에 객체를 추가하거나 제거할 수 있습니다. 그뿐만 아니라 Consumer 클러스터에 대한 권한을 부여하거나 취소할 수도 있습니다.

```sql
-- Producer 클러스터
-- salesshare라는 DATASHARE 생성
-- 같은 계정 내 다른 클러스터와의 공유라면 설정 안해도 됨 (publicaccessible 값 : false)
-- 다른 계정과의 공유라면 설정 필요 (publicaccessible 값 : true)
CREATE DATASHARE salesshare [SET publicaccessible = false];

-- public이라는 schema 추가
ALTER DATASHARE salesshare ADD SCHEMA public;

-- salesshare라는 DATASHARE에 데이터를 공유할 테이블 public.tickit_sales_redshift 추가 
ALTER DATASHARE salesshare ADD TABLE public.tickit_sales_redshift;

-- 권한 부여
-- Consumer 클러스터의 Namespace는 아래 쿼리를 실행해서 얻는 값
GRANT USAGE ON DATASHARE salesshare TO NAMESPACE 'CONSUMER 클러스터의 NAMESPACE';

-- Consumer 클러스터
-- NAMESPACE 검색
SELECT current_namespace;
```

&nbsp;위와 같이 간단하게 Datashare를 생성하고 공유하고자 하는 테이블을 Datashare에 추가하는 작업을 살펴봤습니다. `NAMESPACE`의 경우 Consumer 클러스터에서 검색하여 알아내도록 합니다.

<br>

### DataShare 메타데이터

Redshift는 생성된 Datashare에 대한 정보를 볼 수 있도록 메타데이터를 제공합니다. 

<br>

#### SVV_DATASHARES

사용자 클러스터에서 생성되고 (`OUTBOUND`) 다른 클러스터에서 공유되는 (`INBOUND`) datashare 목록을 확인할 수 있습니다.

|열 이름|데이터 형식|설명|
|-|-|-|
|share_name|varchar(128)datashare의 이름입니다.|
|share_id|integer|datashare의 ID입니다.|
|share_owner|integer|datashare의 소유자입니다.|
|source_database|varchar(128)|이 datashare의 원본 데이터베이스입니다.|
|consumer_database|varchar(128)|이 datashare에서 생성되는 소비자 데이터베이스입니다.|
|share_type|varchar(8)|datashare의 형식입니다. 가능한 값은 INBOUND와 OUTBOUND입니다.|
|createdate|시간대 미포함 TIMESTAMP|datashare가 생성된 날짜입니다.|
|is_publicaccessible|bool|공개적으로 액세스 가능한 클러스터에 datashare를 공유할 수 있는지 여부를 지정하는 속성입니다.|
|share_acl|varchar(256)|datashare에 대해 지정된 사용자 또는 사용자 그룹에 대한 권한을 정의하는 문자열입니다.|
|producer_account|varchar(16)|datashare 생산자 계정의 ID입니다.|
|producer_namespace|varchar(64)|datashare 생산자 클러스터의 고유 클러스터 식별자입니다.|
|managed_by|varchar(64)|datashare를 관리하는 AWS 서비스를 지정하는 속성입니다.|

<br>

#### SVV_DATASHARE_CONSUMERS

Consumer 클러스터 목록을 확인할 수 있습니다.

|열 이름|데이터 형식|설명|
|-|-|-|
|share_name|varchar(128)|datashare의 이름입니다.|
|consumer_account|varchar(16)|datashare 소비자의 계정 ID입니다.|
|consumer_namespace|varchar(64)|datashare 소비자 클러스터의 고유 클러스터 식별자입니다.|
|share_date|시간대 미포함 TIMESTAMP|datashare가 공유된 날짜입니다.|

<br>

#### SVV_DATASHARE_OBJECTS

클러스터와 공유되고 있는 모든 datashare 객체 목록을 확인할 수 있습니다. 이는 슈퍼 유저에게만 공개됩니다.

|열 이름|데이터 형식|설명|
|-|-|-|
|share_type|varchar(8)|지정된 datashare의 형식입니다. 가능한 값은 OUTBOUND와 INBOUND입니다.|
|share_name|varchar(128)|datashare의 이름입니다.|
|object_type|varchar(64)|지정된 객체의 형식입니다. 가능한 값은 schema, table, view, late binding view, materialized view 및 function입니다.|
|object_name|varchar(512)|객체의 이름입니다. 객체 이름은 schema1.t1과 같은 스키마 이름을 포함하도록 확장됩니다.|
|producer_account|varchar(16)|datashare 생산자 계정의 ID입니다.|
|producer_namespace|varchar(64)|datashare 생산자 클러스터의 고유 클러스터 식별자입니다.|
|include_new|bool|지정된 스키마에서 생성된 향후 테이블, 뷰 또는 SQL 사용자 정의 함수(UDF)를 datashare에 추가할지 여부를 지정하는 속성입니다. 이 파라미터는 OUTBOUND datashare와 datashare의 스키마 형식에만 관련이 있습니다.|