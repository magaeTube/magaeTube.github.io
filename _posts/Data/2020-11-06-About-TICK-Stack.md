---
title:  "[Data Engineering] TICK Stack이란"
date:   2020-11-06 00:00:00 +0900
categories:
  - Data
  - DataOthers
tags:
  - Data
toc: true
toc_sticky: true
toc_label: "TICK Stack"
header-img: https://user-images.githubusercontent.com/78892113/208623739-3d766675-2ba6-4d05-b1e0-b735a445b544.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
데이터를 효과적으로 수집하고 저장하면서 시각화까지 제공해주는 솔루션들은 여러가지가 있습니다. 기존에는 ELK(Elasticsearch + Logstash + Kibana) Stack을 이용해보았는데 이번에는 TICK Stack을 살펴보겠습니다.
{: .notice--info}

<br>

## TICK Stack

`TICK Stack`은 Influxdata라는 회사에서 제공하는 오픈소스 솔루션입니다. 여기에서 TICK은 **Telegraf + InfluxDB + Chronograf + Kapacitor**의 앞글자를 따서 만든 것을 말합니다. ELK Stack과 마찬가지로 각각 다른 역할을 수행하는 기술들을 합쳐놓은 것을 말합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/208712887-edf7de3f-1541-47e4-9c79-c3bce7ac1211.png){: .align-center}{: width="80%" height="80%"}

<br>

각각 다른 역할로 데이터를 효과적으로 수집하고 저장하면서 시각화시켜주는 역할을 합니다.

<br><br>

### Telegraf

가장 앞단에 있는 `Telegraf`입니다. `Telegraf`는 데이터를 수집하기 위한 **서버 에이전트**입니다. 수행 중인 시스템에서 직접 데이터를 가져오던지 API를 통해서도 데이터를 가져오는 형태로 작동합니다. MongoDB, MySQL, Redis 등 다양한 데이터베이스와 Kafka와 같은 메시지 큐, IoT 등 약 120개 정도의 플러그인을 제공합니다.

<br><br>

### InfluxDB

`InfluxDB`는 오픈소스 **시계열 데이터베이스(TSDB)**입니다. Go 언어로 작성되었으며 모니터링, metrics, 센터 데이터, 실시간 분석 등 시계열 데이터의 저장과 검색에 최적화되어 있습니다.

<br><br>

### Chronograf

`Chronograf`는 **시각화 오픈소스**입니다. 기본적으로 데이터 시각화를 위해 대시보드를 제공하고 모니터링과 알람, 자동화를 제공합니다. 

<br><br>

### Kapacitor

`Kapacitor`는 **데이터처리 엔진**입니다. InfluxDB로부터 streaming 데이터와 batch 데이터 모두 처리 가능합니다. 일반적인 로직이나 자체적으로 만든 기능과 연동하여 수행할 수 있습니다. HipChat, OpsGenie, Alerta, Sensu, PagerDuty, Slack 등과 연동하여 사용할 수 있습니다. 