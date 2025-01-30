---
title:  "[Apache Hive] SerDe - 서데"
date:   2025-01-31 00:00:00 +0900
categories:
  - Data
  - Hive
tags:
  - Data
  - Hive
  - SerDe
toc: true
toc_sticky: true
toc_label: "SerDe"
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/699e3285-1cf6-4f4d-afc2-1de15161a8d8
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;Apache Hive를 이용하다보면 `SerDe`를 접하게 되는데 이에 대해 알아봅니다.
{: .notice--info}

<br>

![Hive](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/699e3285-1cf6-4f4d-afc2-1de15161a8d8){: .align-center}{: width="50%"}

<br><br>

## SerDe
&nbsp;`SerDe`는 **Serializer (직렬화)**와 **Deserializer (역직렬화)**를 합쳐놓은 형태로 Hive가 데이터를 해석하고 처리하는 방법을 말합니다. 파일을 읽을 때 미리 정해놓은 파일 포맷과 Serializer, Deserializer를 이용해서 데이터를 읽고 쓴다는 것이죠. 여기서 Serializer와 Deserializer는 다음과 같은 역할을 합니다.

* Serializer (직렬화) : Row 데이터를 Key-Value 형태로 변환하여 파일에 저장함.
* Deserializer (역직렬화) : 파일에서 읽은 데이터를 하이브의 내부 객체로 변환함.  
  
&nbsp;즉 위의 두 가지를 통해 SerDe 프로세스는 다음과 같이 진행된다는 것이죠.
* 읽기 과정
  * HDFS (또는 S3) 파일 --> InputFileFormat --> Key-Value --> Deserializer --> Row Object
* 쓰기 과정
  * Row Object --> Serializer --> Key-Value --> OutputFileFormat --> HDFS (또는 S3) 파일

<br>

## Hive 기본 SerDe 종류
&nbsp;Apache Hive에서는 다음의 7가지 기본 `SerDe`를 제공합니다.

* Avro, ORC, RegEx, Thrift, Parquet, CSV, JsonSerDe

&nbsp;위 종류는 테이블 설정할 때 `STORED AS` 절에 지정된 파일 포맷에 따라 자동으로 선택됩니다. 이 외에 커스텀 SerDe를 직접 작성해서 이용할 수도 있습니다.

<br>

## SerDe 작성 쿼리
&nbsp;SerDe 종류에 따라 `ROW FORMAT SERDE`와 `INPUTFORMAT`, `OUTPUTFORMAT` 작성법을 기록해놓습니다. Avro, ORC, Parquet은 SerDe, Input, Output 포맷이 설정되고 나머지는 기본 LazySimpleSerDe를 따라갑니다.

<br>

```sql
-- Avro
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.avro.AvroSerDe'
STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.avro.AvroContainerInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.avro.AvroContainerOutputFormat'

-- ORC
ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.orc.OrcSerde'
STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.orc.OrcInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.orc.OrcOutputFormat'

-- Parquet
ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe'
STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'

-- TEXTFILE
STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.IgnoreKeyTextOutputFormat'
```