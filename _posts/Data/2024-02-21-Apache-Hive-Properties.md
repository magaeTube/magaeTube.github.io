---
title:  "[Apache Hive] 속성"
date:   2024-02-21 00:00:00 +0900
categories:
  - Data
  - Hive
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

* <a href="https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties">https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties</a>

|속성명|기본값| 설명                                                                      |버전|
|----|----|-------------------------------------------------------------------------|--|
|hive.compute.query.using.stats|false| 통계 정보 수집을 위한 설정으로 min, max, count(1) 같은 정보를 메타스토어에 저장함. 기본 통계정보를 수집하기 위해 `hive.stats.autogather`를 true로 설정해야 합니다. |0.13.0~|
|hive.exec.dynamic.partition|0.9.0 이전: false, 0.9.0 이후: true| DML/DDL에 파티션 동적 할당을 허용할지 말지 설정                                          |0.6.0~|
|hive.exec.dynamic.partition.mode|strict| `"strict"` 모드에서는 적어도 하나의 정적 파티션을 지정해야 함. `"nonstrict"` 모드에서는 동적으로 허용됨.  |0.6.0~|
|hive.exec.max.dynamic.partitions.pernode|100| 노드별 동적 파티션 생성 개수                                                        |0.6.0~|
|hive.exec.parallel|false| Job을 병렬로 실행할지 여부 설정                                                     |0.5.0~|
|hive.mapred.supports.subdirectories|false| 실행 중인 Hadoop 버전이 테이블/파티션에 대한 하위 디렉터리를 지원하는지 여부 설정                       |0.10.0~|
|hive.tez.auto.reducer.parallelism|false| Tez의 자동 병렬 reducer 속성을 설정할지 여부                                          |0.14.0~|
|hive.mapred.mode|~2.x: nonstrict, 2.x: strict|`"strict"` 또는 `"nonstrict"`. strict 모드에서는 파티션 처리된 테이블을 조회할 때나 group by, order by를 처리할 때 where 조건이 없으면 처리할 수 없음.|0.3.0~|
|mapred.output.compress||맵리듀스 처리 결과 압축 여부||
|mapred.output.compression.codec||맵리듀스 처리 결과 압축 방식||
|hive.exec.compress.output||하이브 처리 결과 압축여부||
|hive.execution.engine||하이브 실행 엔진. `mr`, `tez`, `spark` 가능||
|mapred.job.queue.name||실행 엔진이 `mr`일 때 스케줄러의 큐 이름 설정||
|tez.queue.name||실행 엔진이 `tez`일 때 스케줄러의 큐 이름 설정||
|spark.job.queue.name||실행 엔진이 `spark`일 때 스케줄러의 큐 이름 설정||
|hive.enforce.bucketing||`true` 또는 `false`. Hive Bucketing 설정||
|hive.vectorized.execution.enabled||`true` 또는 `false`. Hive 벡터화 사용 여부||
|hive.vectorized.execution.reduce.enabled||`true` 또는 `false`. 벡터화 reduce 사용 여부||
|hive.vectorized.execution.reduce.groupby.enabled||`true` 또는 `false`. reduce group by 사용 여부||
|hive.cbo.enable||`true` 또는 `false`. CBO 적용 여부||
|hive.compute.query.using.stats||`true` 또는 `false`. 통계정보 활용 여부||
|hive.stats.fetch.column.stats||`true` 또는 `false`. 컬럼 통계정보 여부||
|hive.stats.fetch.partition.stats||`true` 또는 `false`. 파티션 통계정보 여부||
|hive.support.concurrency||`true` 또는 `false`. 병렬 트랜잭션 허용 여부||
|hive.stats.autogather||`true` 또는 `false`. 통계정보 수집 여부||
|hive.stats.column.autogather||`true` 또는 `false`.||
|hive.strict.timestamp.conversion||`true` 또는 `false`. Date/Timestamp를 Numeric으로 변환할 수 있게 제한을 둘지 여부||
|hive.exec.compress.intermediate|`false`|HIVE 처리 중간 과정에서 발생하는 파일을 압축할지 여부|0.2.0|
|hive.intermediate.compression.codec||HIVE 처리 중간 과정에서 사용할 압축 코덱||
|hive.exec.reducers.bytes.per.reducer|256MB|리듀서당 사이즈|0.14.0|
|hive.exec.reducers.max|1009|리듀서의 최대 개수|0.14.0|
|hive.strict.checks.large.query|true|order by 개수에 대한 제한을 걸지 않고 파티션 테이블에 필터링을 하지 않는 경우 제한됨||
|hive.blobstore.use.output-committer||||
|tez.grouping.min-size|16 MB|Tez 엔진의 매퍼 최소 사이즈||
|tez.grouping.max-size|1 GB|Tez 엔진의 매퍼 최대 사이즈||