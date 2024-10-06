---
title:  "[AWS 자격증 도전기] Certified Data Engineer Associate - 2. 데이터 엔지니어링 기초"
date:   2024-09-12 00:00:00 +0900
categories:
  - Data
  - DataEngineering
tags:
  - Data
  - AWS
toc: true
toc_sticky: true
toc_label: "데이터 엔지니어링 기초"
header-img: https://github.com/user-attachments/assets/8d898a2b-8562-4907-a0a5-304207a69c6d
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;자격증을 공부하고 있는데 기록은 거의 안하고 있었네요. 천천히 하나씩 기록해보겠습니다. 간단하게 정리하는 식으로 진행합니다. 첫번째로는 데이터엔지니어링 기초입니다.
{: .notice--info}

<br>

![AWS](https://github.com/user-attachments/assets/8d898a2b-8562-4907-a0a5-304207a69c6d){: .align-center}{: width="60%"}

<br><br>

## 데이터 타입
* Structured Data 
  * 정의된 스키마 구조로 구성된 정형 데이터
  * 데이터베이스 테이블, CSV, 스프레드시트
* Unstructured Data
  * 사전 정의된 구조나 스키마가 없는 비정형 데이터
  * 텍스트 파일, 비디오/오디오 파일, 이미지
* Semi-Structured Data
  * 정형 데이터처럼 구조화되어 있지 않지만 태그, 계층과 같은 구조의 반정형 데이터
  * XML과 JSON 파일, 로그 파일

<br><br>

## 빅데이터 3V
* Volume
  * 데이터의 양과 사이즈
  * 기가바이트부터 페타바이트, 그 이상의 사이즈
* Velocity
  * 신규 데이터를 수집하고 프로세스하는 속도
  * 고사양의 Velocity를 요구하는 것은 Real-Time 또는 NRT (Near Real-Time)으로 다뤄야함.
* Variety
  *  다양한 타입의 데이터
  * 정형, 반정형, 비정형 데이터

<br><br>

## Data Warehouses vs Data Lakes
* Data Warehouse
  * 각기 다른 소스에서 온 데이터가 구조화된 포맷으로 저장된 중앙화된 레파지토리
  * ETL Process, Star / Snowflake 스키마
  * Amazon Redshift, Google BigQuery, Azure SQL Data Warehouse
* Data Lake
  * 정형/비정형/반정형 데이터를 포함한 원래의 포맷을 가진 어마어마한 대량의 데이터를 가진 스토리지
  * 사전 정의된 스키마가 필요 없음.
  * Amazon S3, Azure Data Lake, HDFS
* Data Lakehouse
  * Data Lake + Data Warehouse
  * 정형/비정형 데이터
  * Schema-on-write & Schema-on-read 허용
  * AWS Lake Formation, Delta Lake
* Data Mesh
  * 데이터 거버넌스
  * 각 도메인 팀이 데이터 프로덕트를 가지는 형태. 도메인 기반 데이터 관리
* ETL
  * Extract, Transform, Load
  * AWS Glue, Amazon MWAA, AWS Step Functions

<br><br>

## 데이터 파일 포맷
* CSV
  * Comma-Separated Values
  * 각 라인은 행에 속하고 콤마(,)를 기준으로 나눠진 데이터들은 열에 해당하는 테이블 형태의 텍스트 기반 포맷
  * 작은 데이터셋에 적합하고 가시적으로 파악할 수 있는 데이터
  * SQL기반 데이터베이스, 엑셀, 파이썬, R
* JSON
  * 텍스트 기반에 사람이 읽을 수 있는 포맷
  * Key-Value로 이루어진 정형 또는 반정형 데이터
  * 웹 서버와 클라이언트 사이의 데이터 교환
* Avro
  * 데이터와 스키마를 모두 저장하는 바이너리 포맷
  * 빅데이터와 리얼타임 프로세싱에서 사용 가능
  * 시스템 간에 데이터 전송에 있어 직렬화가 편함
  * Apache Kafka, Spark, Flink, Hadoop Ecosystem
* Parquet
  * 분석을 위해 최적화된 열 기반 스토리지 포맷
  * 효율적인 압축과 인코딩
  * I/O 운영과 최적화가 필요한 분산 시스템에 데이터를 저장할 때 필요
  * Hadoop, Spark, Hive, Impala, Redshift Spectrum

<br>

### Data Lineage
* Source부터 최종 목적지까지 데이터 생애주기를 통한 데이터의 흐름과 Transformation
* 에러를 트래킹하기 좋음
* 데이터가 어떻게 움직이고 변환되는지 이해가 쉬움

<br>

### Schema Evolution
* 현재 프로세스와 시스템의 방해 없이 데이터셋의 스키마를 변경할 수 있음
* 이전 데이터와의 호환성이 좋음
* Glue Schema Registry

<br>

### Data Skew
* 분산 처리 시스템에서 여러 노드 또는 파티션 간에 데이터 밸런스가 맞지 않는 상태를 말함. 특정 노드에 데이터가 많은 상태
* 발생 원인
  * 적절하지 않은 파티셔닝 전략
  * 일시적인 skew
* 해결책
  * Adaptive Partitioning
  * Salting
  * Repartitioning
  * Sampling
  * Custom Partitioning

<br>

### Data Validation and Profiling
* Completeness
* Consistency
* Accuracy
* Integrity

<br><br>

## SQL
### Aggregation
* COUNT
* SUM
* AVG
* MAX / MIN

<br>

### JOIN
* INNER JOIN
* LEFT OUTER JOIN
* RIGHT OUTER JOIN
* FULL OUTER JOIN
* CROSS OUTER JOIN

<br><br>

## GIT
### Common git commands
* git init
* git config
* git clone
* git status
* git add `filename`
* git commit -m "Commit Message"
* git log

<br>

### Branching with git
* git branch
* git checkout `branchname`
* git merge `branchname`
* git branch -d `branchname`

<br>

### Remote repositories
* git remote add `name` `url`
* git remote
* git push `remote` `branch`
* git pull `remote` `branch`

<br>

### Undoing changes
* git reset
* git reset --hard
* git revert `commit`

<br>

### Advanced git
* git stash
* git rebase `branch`
* git cherry-pick `commit`

<br>

### Git collaboration and inspection
* git blame `file`
* git diff
* git fetch

<br>

### Git maintenance and data recovery
* git fsck
* git gc
* git reflog