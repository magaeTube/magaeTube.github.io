---
title:  "[Apache Hive] 속성"
date:   2024-02-21 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - Hive
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/699e3285-1cf6-4f4d-afc2-1de15161a8d8
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;Apache Hive를 이용하면서 사용하던 속성을 작성합니다. 무작위로 필요할 때 쓰기 위해 기록해놓은 것입니다.
{: .notice--info}

<br>

![Hive](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/699e3285-1cf6-4f4d-afc2-1de15161a8d8){: .align-center}{: width="50%"}

<br><br>

|속성명|기본값| 설명                                                                      |
|----|----|-------------------------------------------------------------------------|
|hive.compute.query.using.stats|false| 통계 정보 수집을 위한 설정으로 min, max, count(1) 같은 정보를 메타스토어에 저장함.                 |
|hive.exec.dynamic.partition|false| DML/DDL에 파티션 동적 할당을 허용할지 말지 설정                                          |
|hive.exec.dynamic.partition.mode|strict| `"strict"` 모드에서는 적어도 하나의 정적 파티션을 지정해야 함. `"nonstrict"` 모드에서는 동적으로 허용됨.  |
|hive.exec.max.dynamic.partitions.pernode|int값| 노드별 동적 파티션 생성 개수                                                        |
|hive.exec.parallel|false| Job을 병렬로 실행할지 여부 설정                                                     |
|hive.mapred.supports.subdirectories|false| 실행 중인 Hadoop 버전이 테이블/파티션에 대한 하위 디렉터리를 지원하는지 여부 설정                       |
|hive.tez.auto.reducer.parallelism|false| Tez의 자동 병렬 reducer 속성을 설정할지 여부                                          |
|hive.mapred.mode||`"strict"` 또는 `"nonstrict"`. strict 모드에서는 파티션 처리된 테이블을 조회할 때나 group by, order by를 처리할 때 where 조건이 없으면 처리할 수 없음.|
|mapred.output.compress||맵리듀스 처리 결과 압축 여부|
|mapred.output.compression.codec||맵리듀스 처리 결과 압축 방식|
|hive.exec.compress.output||하이브 처리 결과 압축여부|
|hive.execution.engine||`Tez`. 하이브 실행 엔진|
|hive.enforce.bucketing||`true` 또는 `false`. Hive Bucketing 설정|
|hive.vectorized.execution.enabled||`true` 또는 `false`. Hive 벡터화 사용 여부|
|hive.vectorized.execution.reduce.enabled||`true` 또는 `false`. 벡터화 reduce 사용 여부|
|hive.vectorized.execution.reduce.groupby.enabled||`true` 또는 `false`. reduce group by 사용 여부|
|hive.cbo.enable||`true` 또는 `false`. CBO 적용 여부|
|hive.compute.query.using.stats||`true` 또는 `false`. 통계정보 활용 여부|
|hive.stats.fetch.column.stats||`true` 또는 `false`. 컬럼 통계정보 여부|
|hive.stats.fetch.partition.stats||`true` 또는 `false`. 파티션 통계정보 여부|
|hive.support.concurrency||`true` 또는 `false`. 병렬 트랜잭션 허용 여부|
|hive.stats.autogather||`true` 또는 `false`. 통계정보 수집 여부|
|hive.strict.timestamp.conversion||`true` 또는 `false`. Date/Timestamp를 Numeric으로 변환할 수 있게 제한을 둘지 여부|