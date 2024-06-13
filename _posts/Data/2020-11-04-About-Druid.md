---
title:  "[Data Engineering] Apache Druid는.."
date:   2020-11-04 00:00:00 +0900
categories:
  - Data
  - Druid
tags:
  - Data
  - Druid
toc: true
toc_sticky: true
toc_label: "Apache Druid"
header-img: https://user-images.githubusercontent.com/78892113/209270038-0e690c31-c629-404d-9a86-38fe361cb57a.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
데이터 분석을 위해 여러 종류의 데이터베이스를 이용할 수 있는데 이번에는 OLAP 용도로 사용하는 Apache Druid에 대해 알아보겠습니다.  
{: .notice--info}

<br>

![Druid](https://user-images.githubusercontent.com/78892113/209270038-0e690c31-c629-404d-9a86-38fe361cb57a.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# Overview

Apache Druid <a href="https://druid.apache.org/">공식 홈페이지</a>에 나와있는 것을 보면 `Apache Druid`는 **고성능 실시간 분석 데이터베이스**라고 나와있습니다. 실시간 분석을 하기 위해 사용되는 데이터베이스인 것이죠. 또한, 개요로 다음과 같이 소개하고 있습니다.

<br>

## 빠르고 현대적인 분석 데이터베이스

`Druid`는 ad-hoc 분석, 즉각적인 데이터 가시성, 높은 동시성을 요구하는 작업에 탁월합니다. 그뿐만 아니라 유저 경험을 요구하는 강력한 UI를 제공합니다.

<br>

## 기존에 존재하던 데이터와 쉽게 통합

`Druid`는 `Kafka`, `Amazon Kinesis` 같은 메시지에서부터 데이터를 받기도 하고 `HDFS`, `Amazon S3`와 같은 데이터 레이크로부터 데이터를 받기도 합니다. 그 외에도 구조적이거나 반구조적인 데이터 포맷도 지원합니다.

<br>

## 높은 동시성에서 빠르고 정확한 쿼리 제공

`Druid`는 기존의 레거시 솔루션들보다도 높은 성능을 나타냅니다. Hive보다는 **약 100~200배**까지, Presto보다는 **20~60배** 정도까지 빠른 성능을 보이는 것을 알 수 있습니다.

<br>

## 광범위한 적용 가능성

`Druid`는 여러 데이터 타입과 새로운 쿼리 형태에 대해 적용할 수 있습니다. Druid는 실시간과 과거 데이터 모두에 대해 빠르고 ad-hoc 쿼리를 목적으로 만들어졌습니다.

<br>

## 다양한 클라우드 적용

Druid는 퍼블릭, 프라이빗, 하이브리드 클라우드 등 어떠한 형태에 모두 적용할 수 있습니다. 

<br><br><br>

# Features

<br>

![image](https://user-images.githubusercontent.com/78892113/209306644-bbe72e1f-c1c8-4856-bca0-4af3bb207401.png)

`Druid`는 Data Warehouse와 시계열 DB, 검색 시스템의 개념을 모두 합친 분산 데이터 스토어 형태입니다.  

* **Column 기반 스토리지** : Druid는 각 Column을 개별적으로 저장하고 빠른 스캔, 랭킹, 그룹을 지원하는 특정한 쿼리를 필요로 합니다. 
* **기본 검색 색인** : Druid는 빠른 검색과 필터를 위해 역 색인을 생성합니다.
* **스트리밍과 일괄 수집** : Apache Kafka, HDFS, AWS S3 등등 다양한 connector를 지원합니다.
* **유연한 스키마** : Druid는 진화된 스키미와 nested data를 효과적으로 다룹니다.
* **최적화된 Partitioning** : Druid는 기존의 전통적인 데이터베이스보다 똑똑하게 데이터 파티션 작업을 수행합니다.
* **SQL 지원** : Druid는 HTTP 또는 JDBC를 통한 SQL과 JSON 기반 언어를 지원합니다.
* **수평적 확장성** : Druid는 초당 수백만 개의 이벤트를 수집하고 데이터를 유지하고 1초 미만의 쿼리를 제공하는데 사용됩니다.
* **쉬운 운영** : Druid는 서버를 추가하고 제거하면서 자동으로 Scale up 또는 down을 수행합니다. 