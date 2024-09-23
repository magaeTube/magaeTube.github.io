---
title:  "[Apache Spark] 속성"
date:   2024-03-21 00:00:00 +0900
categories:
  - Data
  - Spark
tags:
  - Data
  - Spark
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/33121d67-2121-4304-8bcc-a75ef78d592d
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;Apache Spark를 이용하면서 사용하던 속성을 작성합니다. 무작위로 필요할 때 쓰기 위해 기록해놓은 것입니다.
{: .notice--info}

<br>

![spark](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/33121d67-2121-4304-8bcc-a75ef78d592d){: .align-center}{: width="50%"}

<br><br>

* <a href="https://spark.apache.org/docs/latest/configuration.html">https://spark.apache.org/docs/latest/configuration.html</a>

|속성명|기본값| 설명                                                                      |버전|
|----|----|-------------------------------------------------------------------------|--|
|spark.sql.sources.partitionOverwriteMode|STATIC|파티션 테이블에 INSERT OVERWRITE할 때 지원하는 모드로 `static`과 `dynamic`이 있음. static 모드에서 Spark는 OVERWRITE하기 전 INSERT 구문에서 데이터를 넣고자 하는 파티션과 매핑된 파티션을 전부 삭제합니다. dynamic 모드에서는 파티션을 지우지 않고 단지 overwrite합니다.|2.3.0|
|spark.dynamicAllocation.initialExecutors||dynamic allocation이 설정되어 있을 때 실행할 executor의 초기 개수|1.3.0|
|spark.dynamicAllocation.minExecutors||dynamic allocation이 설정되어 있을 때 실행할 executor의 최소 개수|1.3.0|
|spark.dynamicAllocation.maxExecutors||dynamic allocation이 설정되어 있을 때 실행할 executor의 최대 개수|1.3.0|
|spark.hadoop.orc.overwrite.output.file|true??|||
|spark.sql.legacy.timeParserPolicy|LEGACY??|||
|spark.sql.shuffle.partitions|200|join이나 aggregation을 하기 위한 데이터를 shuffle할 때의 파티션 개수|1.1.0|
|spark.yarn.maxAppAttempts|1|||
|spark.dynamicAllocation.enabled|false|동적 자원 할당을 사용할지 여부||
|spark.driver.maxResultSize|1g|각 Spark action에서 모든 파티션들의 결과 총 사이즈|1.2.0|
|spark.sql.hive.convertMetastoreOrc|true|Orc 파일 데이터를 읽을 때 Spark의 Native ORC Reader를 사용할지 여부. `false`로 하면 Hive의 Reader를 사용하게 됨.||
|spark.sql.parquet.compression.codec||`parquet`를 이용해서 데이터를 저장할 때 압축형태||