---
title:  "[Data 포지션] Data Engineer"
date:   2021-10-14 09:00:00 +0900
categories:
  - Data
  - DataEngineering
tags:
  - Data
  - DataEngineering
toc: true
toc_sticky: true
toc_label: "Data Engineer"
---

<br>

&nbsp;Data Engineer는 Data 분석 또는 경영진의 의사 결정을 위해 각기에 흩어져있는 데이터를 한곳으로 수집하여 저장하고 보여줄 수 있는 Data Pipeline을 구축하는 것이 주 업무입니다. Data가 굉장히 중요해진 만큼 Data 분석은 필수사항이 되었고 이를 효과적으로 하기 위해 Data Engineer가 필요합니다. Data Engineer가 하는 일이 어떤 것이 있고 필요한 역량은 무엇인지 보겠습니다.
{: .notice--info}

<br><br>

# 하는 업무
---

<br>

![markus-spiske-hvSr_CVecVI-unsplash](https://user-images.githubusercontent.com/78892113/137113844-c45b5fd6-403f-41b4-af59-5c302b88af92.jpg){: width="80%"}

Photo by <a href="https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Markus Spiske</a> on <a href="https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  
  
<br><br>

&nbsp;회사마다 Data Engineer와 Backend Engineer 그리고 Data Analyst를 나누는 경계가 모호합니다. 어느 회사는 Data Engineer가 Backend Engineer가 하는 업무(예, API개발, 로그 수집 등)를 하는 경우도 있고 Data Analyst가 하는 업무(예, 데이터 분석 등)를 하는 경우도 있습니다. 업무가 일부 비슷한 부분이 있기도 하고 회사의 조직도나 R&R의 범위를 어떻게 볼 것인지 따라 다르기 때문이죠  
&nbsp;Backend Engineer는 보통 서비스에서 보여지는 데이터를 빠르게 보여주고 효율적으로 다루는 방법을 고민한다면 Data Engineer는 서비스에서 생성되는 데이터를 유실없이 빠르게 축적하거나 사내에 있는 데이터와 외부 데이터를 수집해서 데이터 웨어하우스나 데이터 마트에 축적하는 방법에 대해 고민을 합니다.  
&nbsp;Data Engineer에게 Data Analyst가 하는 업무도 요구하는 것은 아무래도 Data Engineer가 가장 근접하게 같이 일하는 사람이 Data Analyst이기 때문입니다. Data Analyst가 하는 일을 알아야 그들과 소통을 하고 데이터를 효과적으로 제공할 수 있기 때문이죠.  
&nbsp;이렇게 업무가 겹치는 부분이 있는데 이러한 부분을 염두에 두고 공통적으로 Data Engineer가 하는 업무를 보겠습니다.

<br>

## 데이터 파이프라인 구축
---

![victor-UoIiVYka3VY-unsplash](https://user-images.githubusercontent.com/78892113/137110378-b21f0000-c841-4ea1-8f0e-a23060661be0.jpg){: width="80%"}

Photo by <a href="https://unsplash.com/@victor_g?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Victor</a> on <a href="https://unsplash.com/s/photos/pipeline?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>


<br><br>

&nbsp;Data Engineer가 주로 하는 업무입니다. 데이터 파이프라인은 데이터가 발생된 시점부터 시작하여 분석가들이 분석을 하기 위한 상태까지 자동으로 흘러가도록 파이프라인을 생성하는 것입니다. 이러한 파이프라인에는 데이터 수집/처리/적재의 과정이 있습니다. 

​<br>

## 데이터 수집
---

<br>

&nbsp;첫 번째로 데이터 수집입니다. 여기저기 각기 다른 곳에 흩어져있는 데이터를 수집하는 것인데 여기에서 데이터의 종류는 회사마다 다릅니다. 고객 데이터, 외부 데이터, 로그 데이터 등등 회사마다 분석을 요구하는 데이터의 종류와 주기적으로 유입되는 배치 데이터인지 실시간 데이터인지에 따라 나뉘어서 수집 방법도 달라집니다. 

<br><br>

## 데이터 처리 
---

<br>

&nbsp;다음으로는 데이터 처리입니다. 이렇게 수집된 데이터를 데이터 웨어하우스에 저장하기 위해 처리하는 작업을 말합니다. 처리작업에는 ETL작업도 있고 기존의 데이터베이스로는 감당이 안 될 만큼 많은 데이터이다 보니 이를 효과적으로 처리하기 위해 분산처리를 하는 단계입니다.

<br><br>

## 데이터 저장
---

<br>

&nbsp;이렇게 수집된 데이터를 처리하고 나면 데이터를 저장하는 작업을 진행합니다. 저장하는 곳은 보통 분석을 위해 많이 사용하는 데이터 웨어하우스나 데이터 레이크에 저장하는 작업을 진행합니다. 

<br><br>

# 필요 역량
---

<br>

![image-62-1024x588](https://user-images.githubusercontent.com/78892113/137110044-e9546d94-be35-4732-9984-e8a71464f527.png)


<br><br>

## SQL & NoSQL
---

<br>

&nbsp;SQL은 Structured Query Language의 줄임말로 구조화 질의어라고 합니다. 관계형 데이터베이스(MySQL, Oracle, PostgresSQL 등)에 있는 데이터들을 가져오기 위해서는 이 SQL을 알아야 하는데 분석가들이 원하는 데이터를 자유자재로 가져와야되기 때문에 이 역량이 매우 중요합니다. SQL은 여러 관계형 데이터베이스뿐만 아니라 Google BigQuery, AWS RedShift 등 여러 데이터웨어하우스에서도 요구하는 역량이기 때문에 중요하다고 볼 수 있습니다.  
&nbsp;비정형 데이터가 생기다보니 기존의 관계형 데이터베이스로는 커버할 수 없기에 나온 기술이 NoSQL DB입니다. Data Engineer는 이러한 NoSQL DB에서도 데이터를 추출해야하기 떄문에 어느정도 다룰줄 알아야 합니다. 대표적인 NoSQL DB에는 MongoDB와 Redis가 있습니다.

<br><br>

## 프로그래밍 언어
---

<br>

&nbsp;Data Engineer에게 프로그래밍 언어에 대한 역량도 필요합니다. 데이터를 수집하거나 처리하여 저장하는 일 또는 분산처리하는 기술을 다루기 위해 프로그래밍 언어 역량을 요구합니다. 채용 공고를 봤을 때는 기본적으로 Python, Java, Scala 중 한 개 이상의 역량을 요구합니다. 분산처리 기술로 많이 사용되는 Hadoop은 Java 프로그래밍을 해야 할 때가 있고 Spark은 Python 또는 Scala를 이용합니다. 그뿐만 아니라 Python의 경우 웹 크롤링을 이용한 데이터 수집으로 사용되는 경우도 있습니다. 이렇게 분산 처리 기술을 이용하기 위해 기본적인 프로그래밍 언어 역량을 요구하고 있습니다. Python, Java, Scala 외에도 Go, Kotlin, NodeJS 등 회사마다 다양한 프로그래밍 역량을 요구하고 있습니다.

<br><br>

## 분산처리
---

<br>

&nbsp;스마트폰이 활성화되고 빅데이터라는 개념이 나오면서 기존의 데이터 처리 방식으로는 빅데이터를 다루기에 한계가 있었습니다. 그렇기 때문에 빅데이터를 처리하는데 분산처리 기술이 주목을 받게 되었습니다. Hadoop Ecosystem이나 Apache Spark, Apache Kafka 등의 기술들이 분산처리를 하는데 필요한 기술들로 이러한 기술에 대한 역량을 요구하고 있습니다.

<br><br>

## 스트리밍 데이터 관리
---

<br>

&nbsp;데이터를 수집할 때 배치성(주기적인 일정으로 수집) 데이터가 있고 실시간으로 쌓이는 데이터(로그 데이터)가 있습니다. 실시간으로 데이터를 처리하고 분석해야하는 경우가 있습니다. 이러한 회사에서는 스트리밍 데이터를 안전하게 운용하는 역량을 요구합니다. 일반적으로 Apache Kafka, AWS Kinesis 등의 기술을 많이 이용합니다.  

<br><br>

## 워크플로우 관리
---

<br>

&nbsp;데이터 파이프라인을 구축하고 나면 이러한 데이터 파이프라인을 관리하기 위한 기술을 제공합니다. 워크 플로우인데 현재 파이프라인이 어느 단계를 수행 중이고 어느 단계에서 오류가 발생하였는지 모니터링하고 파악하여 재시도하는 등 발 빠른 대처를 할 수 있도록 도와줍니다. 대부분의 회사들이 가장 많이 이용하는 것이 Apache Airflow입니다. Airflow는 Python 기반 라이브러리인데 워크 플로우를 Web UI로 제공하여 사용자들이 보기 쉽게 제공하고 있습니다. 여러 기능을 제공하기 때문에 많이 이용하고 있습니다. 그 외에는 Luigi를 이용하는 회사가 있습니다.

<br><br>

## 클라우드 / 컨테이너
---

<br>

&nbsp;이제는 클라우드를 안 쓰는 회사가 거의 없을 만큼 클라우드가 중요해졌습니다. 아무래도 빅데이터는 데이터의 양이 회사마다 천차만별이기 때문에 그만큼 리소스도 중요하고 부담이 되는데 클라우드가 이러한 부담을 줄여줍니다. 그리고 AWS나 GCP에서 Hadoop이나 Spark 등 여러 빅데이터 기술들을 기반한 서비스도 제공되기 때문에 이러한 스트리밍이나 데이터 웨어하우스 같은 서비스들을 직접 구현하기보다는 클라우드 서비스를 이용하는 회사도 늘어나고 있습니다.

&nbsp;클라우드만큼 필수적인 요소가 되고 있는 것이 컨테이너입니다. Docker와 Kubernetes를 이용해서 컨테이너 환경을 구축하고 있습니다. 아무래도 서버에 직접 구축을 하는 것보다 Docker를 이용한 컨테이너 환경을 구축하는 것이 더 편리하고 효율적이기 때문입니다. 컨테이너 환경이 구축되어 있는 회사에서는 해당 컨테이너를 다룰 수 있는 역량을 요구하고 있습니다.

<br><br>

## 데이터 분석 / 시각화
---

<br>

&nbsp;데이터 시각화 툴이나 BI 툴을 다뤄본 역량을 요구하는 회사들도 있습니다. 분석과 시각화에 대한 역량은 Data Engineer에게 필수적인 요소는 아니지만 Data Analyst와 협업을 많이 하기 때문에 그들과 원활한 의사소통을 나누기 위해 시각화 툴과 BI 툴에 대한 역량도 요구합니다. 때로는 경영진의 의사소통을 돕기 위해 Data Engineer가 직접 만들기도 합니다. 

<br><br>