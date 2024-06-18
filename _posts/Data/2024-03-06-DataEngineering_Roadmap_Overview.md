---
title:  "[Data Engineering Roadmap] 1. 개요"
date:   2024-03-06 00:00:00 +0900
categories:
  - Data
  - DataEngineering
tags:
  - Data
toc: true
toc_sticky: true
toc_label: "Overview"
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/cd41dda4-ecce-48bb-b333-f7dc528df3a2
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;Data Engineer가 되기 위해 Data Engineering Roadmap을 살펴보고 하나씩 공부해나가는 시간을 가져보려고 합니다.
{: .notice--info}

<br>

![DataEngRoadmap](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/cd41dda4-ecce-48bb-b333-f7dc528df3a2)

<br>

# 개요

&nbsp;`Data Engineer`가 되기 위해서 또는 Data Engineering에 관심이 있기에 공부하려는 분들 중에서 위와 같은 Roadmap를 보신 분들이 꽤 있으실 것이라고 생각합니다. 처음 이것을 보고 들었던 생각은 <u>이 많은 것을 다 공부해야 하는가? 언제 다하지?</u> 하는 의문입니다. 결론부터 말씀드리자면 **아닙니다.** 각자 또는 회사가 처한 상황에 따라 필요한 것들이 다르기 때문에 모든 것을 공부할 필요는 없다는 것입니다. 예를 들면 회사가 사용하는 클라우드 플랫폼이 `AWS`냐 `GCP`냐에 따라 공부할 것이 달라지기도 하고 `Scala`를 사용하고 있는지 아닌지에 따라서도 달라질 수 있고 상황이 다 다르기 때문에 모든 것을 공부할 필요는 없고 그때그때 필요할 때 공부하는 것이 맞는다고 생각합니다. 물론 개념적인 범위로 두루두루 아는 것은 좋다고 생각합니다. 왜냐하면 <u>여러 가지를 아는 것은 시야를 넓혀주기 때문에 데이터 플랫폼을 처음 구축하거나 아키텍처를 확장하거나 등의 상황에서 이러한 정보들은 도움</u>이 되기 때문입니다. 그래서 하나씩 개념적인 차원에서 공부하고자 합니다.  

&nbsp;모든 것을 다 할 필요는 없다고 했지만 여러 영역으로 지속적으로 공부해야 하는 것은 맞습니다. 위 이미지에서 나타난 영역이 많기 때문에 이를 다음과 같이 나눠보았습니다.  

* Programming Language
* SQL & NoSQL
* Data Warehouse & Data Lake
* Distributed Computing
* Workflow Orchestration
* Streaming Data Processing
* Cloud Computing
* ETC - Monitoring / Data Visualization
* ETC - CI/CD
* ETC - MLOps

&nbsp;위와 같이 영역을 나누었는데 계속 말씀드렸던 것처럼 모든 영역을 다 알 수는 없고 위 영역들을 전체적으로 훑어보고 필요한 것들은 깊게 공부하면 됩니다.

<br><br>

# Programming Language
&nbsp;엔지니어링의 기본은 `프로그래밍 언어`입니다. 프로그래밍 언어의 종류는 매우 여러 가지인데 Data Engineering에서 주로 사용하는 언어는 `Python`, `Java`, `Scala`입니다. 하둡 기반의 오픈소스들은 Java로 되어있는 것들이 많고 데이터 엔지니어링 중 가장 핫한 스파크는 Scala로 된 것이 많기에 스칼라도 중요하게 생각했습니다. 그러나 최근에는 특히 많이 사용하는 것은 Python입니다. 전에는 Python이 Java와 Scala에 비해 성능적으로 많이 떨어진다고 했지만 최근에는 성능상으로 많이 따라왔기 때문에 스파크도 Python을 많이 이용하기도 하고 Airflow나 Superset 같은 오픈 소스들도 Python으로 구성되어 있습니다.

<br><br>

# SQL & NoSQL
&nbsp;Data Engineering에서 가장 중요하고 기본이 되는 것이 `SQL`이라고 생각합니다. 데이터 엔지니어링 다음으로 작업하는 데이터 분석이나 AI를 위해서는 데이터를 추출하는 작업이 시작인데 이때 사용하는 것이 SQL이고 모든 데이터베이스, 데이터 웨어하우스 등에서 사용하는 기본이기 때문에 중요합니다.

<br><br>

# Data Warehouse & Data Lake
&nbsp;데이터를 추출하는 작업을 진행했다면 다음은 저장하는 작업입니다. 기존의 RDBMS로는 빅데이터를 담을 수 없었기에 이를 해결하기 위해 `Data Lake`와 `Data Warehouse`가 등장하게 되었습니다. 

<br>

# Distributed Computing
&nbsp;`Distributed Computing`은 분산 처리 기술을 말합니다. 분산 처리가 필요한 이유는 데이터가 많아지다 보니 하나의 컴퓨팅 기술로는 한계가 있었던 것이죠. 그렇기에 여러 대의 컴퓨팅을 이용한 분산 처리가 필요하게 되었고 데이터 엔지니어링에서 이것이 제일 중요한 기술 중 하나로 여겨졌습니다.

<br>

# Workflow Orchestration
&nbsp;데이터 엔지니어링은 데이터를 추출하여 프로세싱하고 저장하는 등의 과정을 거치는데 이를 매번 수동으로 할 수는 없습니다. 한 번의 작업만 필요한 경우 수동으로 하면 되지만 주기적으로 필요한 작업의 경우 크론을 이용하기도 합니다. 하지만 점점 데이터 파이프라인의 수가 증가하고 직관적이며 정교한 작업을 하기 위해 툴이 필요하죠. 대표적으로는 `Apache Airflow`가 있습니다.

<br>

# Streaming Data Processing
&nbsp;데이터를 처리하는 방법에는 주기적으로 처리하는 것과 실시간으로 처리해야 하는 경우가 있습니다. 실시간으로 처리해야 할 때 이러한 `Streaming` 기술들을 이용하면 됩니다.

<br>

# Cloud Computing
&nbsp;`Cloud Computing`은 스타트업이던 대기업이던 기업의 크기에 관계없이 많은 기업들이 사용하고 있습니다. 가격을 생각해서 온프레미스로 운영하는 회사들도 있지만 비용을 투자하는 대신 유지 보수나 관리의 측면에서 이점을 가져오기 위해 클라우드를 사용하는 회사들도 많습니다. 그렇기에 모든 것을 신경 쓸 수 없는 상황이라면 클라우드를 이용하는 것이 더 좋다고 봅니다.
