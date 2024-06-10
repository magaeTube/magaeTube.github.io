---
title:  "[Apache Iceberg] Big Data File Format"
date:   2024-06-11 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - Iceberg
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/33121d67-2121-4304-8bcc-a75ef78d592d
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;Apache Iceberg 테이블을 이용하게 되었는데 이 Iceberg가 무엇이고 어떤 것이 좋은지 알아봅니다.
{: .notice--info}

<br>


<br>

# Apache Iceberg란 
&nbsp;Apache Iceberg는 Apache Hive에서 발생하는 규모와 성능의 문제를 해결하기 위해 나온 오픈소스입니다. Netflix에서 처음으로 개발되어 Apache 인큐베이터 프로젝트로 오픈소스화되었고 2~3년에 걸쳐서 인큐베이터 프로젝트를 마무리할 수 있었습니다. Iceberg는 대용량의 분석 데이터세트를 위한 **테이블 포맷**입니다. Spark, Trino, PrestoDB, Flink, Hive와 Impala와 같은 컴퓨팅 엔진에서 SQL을 이용하여 실행될 수 있습니다. Iceberg에는 대표적인 3가지의 특징이 있습니다.

<br>

## Schema 진화
&nbsp;Hive를 사용하다보면 불편한 점은 Schema 변경 과정입니다. 예를 들어 신규 컬럼을 추가하고자 할 때 Hive에서는 신규 컬럼의 위치를 정할 수 없고 무조건 마지막 위치에 추가됩니다. 만약에 원하는 위치에 추가하고 싶다면 아래와 같은 절차를 거쳐야 합니다.
* 기존 테이블 백업
* 원하는 컬럼 위치로 배치된 신규 테이블 생성 
* 신규 테이블에 기존 테이블 데이터 백업
* 기존 테이블 삭제 및 신규 테이블 이름 변경
위와 같은 절차는 기존 데이터의 변경이 들어가기 때문에 위험의 요지도 있고 기존 데이터가 많으면 시간도 오래 걸리기 때문에 리스크가 있을 수 있습니다. 하지만 Iceberg는 이러한 복잡한 과정이 필요없이 원하는 위치에 생성하실 수 있습니다. 이는 Iceberg가 테이블의 메타데이터를 업데이트하는 방식이기 때문에 가능한 특징입니다. 그 외에도 이름 변경, 데이터 타입 변경 등을 데이터 변경없이 수행할 수 있습니다.

<br>

## 히든 파티셔닝
&nbsp;Hive에서는 파티션을 이용할 때 명시를 해야 합니다. 테이블을 생성할 때나 입력을 할 때나 명시를 해야한다는 특징이 있습니다. 그러나 Iceberg의 경우 히든 파티셔닝을 제공하여 사용자가 직접 파티션을 신경쓰지 않도록 설계되어 있어 쿼리 성능을 최적화합니다.

<br>

## Time Travel & Rollback
&nbsp;Iceberg는 데이터의 각 스냅샷을 관리합니다. 그렇기에 과거 시점의 데이터를 조회할 수도 있고 데이터를 롤백할 수 있도록 제공하고 있습니다. Time Travel은 Iceberg가 스냅샷을 관리하기 때문에 특정 시점의 데이터 상태를 조회할 수 있도록 제공하는 기능입니다. 이를 통해 데이터 변경 이력을 추적하고 분석할 수 있습니다.

<br>

## ACID 트랜잭션
&nbsp;기존에 Hive는 ORC 파일 포맷을 이용할 때만 ACID 트랜잭션을 지원했지만 Iceberg에서는 분산 환경에서도 ACID 트랜잭션을 지원하여 데이터 일관성과 무결성을 보장합니다.

<br><br>

# Apache Iceberg와 Apache Parquet 비교
&nbsp;File Format으로는 기존에 Parquet가 많이 사용되고 있습니다. 근데 Iceberg와 몇 가지 특징으로 비교를 해봅니다.

<br>

## 스키마 진화
&nbsp;Iceberg는 위에서 말씀드렸던 것처럼 강력한 스키마 진화 기능을 제공합니다. 물론 Parquet도 Hive보다는 유연하게 스키마 변경을 할 수 있지만 Iceberg처럼 강력하다고 볼 수 없습니다. 예를 들면 Parquet에서는 Nested 구조를 업데이트하거나 파티셔닝 사양을 업데이트하는 것을 제공하지 않습니다. 그렇기에 스키마 진화의 측면에서는 Iceberg가 더 강력하다고 볼 수 있습니다.

<br>

## 성능
&nbsp;성능에서는 두 가지 측면으로 비교합니다. 첫 번째로는 Predicate Pushdown입니다. Iceberg와 Parquet 모두 Predicate Pushdown을 제공하는데 이는 데이터를 필터링할 때 프로세싱 레벨에서 처리하는 것보다 스토리지 레벨에서 처리하여 필요한 데이터만 가져오기 때문에 I/O를 줄이고 쿼리 성능을 올릴 수 있도록 하는 방법입니다. Parquet는 Columnar Storage와 함께 Predicate Pushdown이 잘 이뤄지지만 Iceberg는 파티션 사양을 기반으로 한 Partition Pruning 작업을 통해 I/O를 줄이고 쿼리를 향상시키는데 더 효율적입니다.  
&nbsp;두 번째로는 Vectorized Reads입니다. Parquet는 Vectorized Reads를 제공하는데 이는 데이터를 한 행 단위가 아니라 일괄 처리하여 더 빠르게 데이터를 처리할 수 있도록 합니다. 반면에 Iceberg는 현재 해당 기능을 제공하고 있지 않지만 미래에 개선이 될 것으로 예상됩니다.  
&nbsp;이와 같이 성능적인 측면에서는 아직은 Parquet가 더 좋다고 볼 수 있을 것 같습니다.

<br>

## 스토리지


<br><br>

# 참고 사이트
* <a href="https://www.decube.io/post/what-is-apache-iceberg-versus-parquet">https://www.decube.io/post/what-is-apache-iceberg-versus-parquet</a>