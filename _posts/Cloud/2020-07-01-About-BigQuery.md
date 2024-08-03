---
title:  "[BigQuery] 구글 빅쿼리 (1) - 소개"
date:   2020-07-01 00:00:00 +0900
categories:
  - GCP
tags:
  - Cloud
  - GCP
  - BigQuery
toc: true
toc_sticky: true
toc_label: "BigQuery 소개"
header-img: https://user-images.githubusercontent.com/78892113/209471556-8c6c325a-8c24-48a0-a12c-4f9ffea0bbc3.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
구글의 빅쿼리(BigQuery)는 구글에서 제공하는 서버리스 클라우드 데이터 웨어하우스입니다. 높은 확장성과 비용 효율성을 갖추고 있다는 것이 특징인데요. 공식 홈페이지에서 제공하는 정보를 기준으로 정리해보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209471556-8c6c325a-8c24-48a0-a12c-4f9ffea0bbc3.png){: .align-center}{: width="70%" height="70%"} 

<br>

* 공식 홈페이지 <a href="https://cloud.google.com/bigquery/?hl=ko">URL</a>

<br><br>

## 주요 기능

주요 기능으로는 ML, BI Engine, GIS가 있습니다.

<br>

### BigQuery ML

BigQuery 내에서 SQL을 이용하여 **`머신러닝(ML)` 모델을 신속하게 빌드 및 운용**할 수 있습니다. `BigQuery ML`에서는 예측용 선형 회귀, 분류용 이진 로지스틱 회귀 2가지 모델 유형을 지원합니다. 만들어진 모델은 Cloud AI Platform 또는 자체 서비스 레이어로 내보냅니다. 

<br>

### BigQuery BI Engine

BigQuery BI Engine은 빠른 속도를 구현하는 **BigQuery용 메모리 내 분석 서비스**로 빠른 쿼리 응답 시간과 높은 동시성으로 분석을 지원합니다. 데이터 스튜디오와 같은 친숙한 도구와 BI 파트너의 데이터 탐색 및 분석을 지원할 예정입니다. 

<br>

### BigQuery GIS

위치 인텔리전스를 활용하여 분석 워크플로우를 강화할 수 있습니다. 공간 데이터를 새로운 관점으로 바라보며 다양한 형태로 데이터 형식을 지원합니다. 

<br><br>

## 특징

`Google BigQuery`에는 여러가지 특징이 있는데 몇가지 살펴보도록 하겠습니다. 

​<br>

### 서버리스 (Serverless)

구글의 모든 리소스 프로비저닝이 **백그라운드에서 실행**되므로 인프라, 보안, 관리에 신경쓸 필요가 없습니다

<br>

### 실시간 분석 (Real-time Analytics)

BigQuery의 고속 스트리밍 삽입 API가 실시간 분석 기반을 제공하고 `Pub/Sub` 및 `Dataflow`를 활용하여 BigQuery로 데이터를 스트리밍할 수 있습니다. 

<br>

### 지리정보 데이터 유형 및 함수 (GIS)

`BigQuery GIS`는 WKT와 GeoJSON 형식의 임의의 점, 선, 다각형, 다중 다각형에 대한 SQL 지원을 제공합니다. 새로운 방식으로 위치 기반 데이터를 확인하거나 새로운 사업 분야를 개척할 수 있습니다. 

<br>

### 데이터 전송 서비스 (Data Transfer Service)

BigQuery Data Transfer Service는 **완전 관리형 방식**으로 Google Marketing Platform, Google Ads, YouTube와 같은 외부 데이터 소스의 데이터를 BigQuery로 전송합니다. 

<br>

### 프로그래매틱 상호작용 (Programmatic Interaction)

`BigQuery`는 간편한 프로그래매틱 액세스와 애플리케이션 통합을 위해 **REST API**를 제공합니다. 라이브러리는 Java, Python, Node.js, C#, Go, Ruby, PHP로 제공됩니다.  
이 외에도 많은 특징이 있는데 공식 홈페이지를 통해 확인해보시길 바랍니다. 

<br><br>

## 가격 책정

데이터 스토리지, 스트리밍 삽입, 데이터 쿼리 비용은 청구되지만 데이터 로드 및 내보내기는 무료로 제공합니다. 

![image](https://user-images.githubusercontent.com/78892113/209472098-99c9a976-e00f-4bd9-ae30-d9933fdc92fe.png)

<br>

이러한 가격은 종량제와 정액제로 이용할 수 있는데 해당 정보도 아래와 같습니다. 매달 BigQuery로 처리되는 첫 1TB는 무료라는게 눈에 띄네요.

![image](https://user-images.githubusercontent.com/78892113/209472130-116b7cae-6352-4600-add2-56079d865349.png)

<br>

이상으로 대략적인 BigQuery에 대한 내용을 살펴보았고 다음번에는 설치 및 세팅 작업을 해보겠습니다. 