---
title:  "[Data Engineering] InfluxDB 소개"
date:   2020-11-06 00:00:00 +0900
categories:
  - Data
  - DataOthers
tags:
  - Data
  - InfluxDB
toc: true
toc_sticky: true
toc_label: "InfluxDB"
header-img: https://user-images.githubusercontent.com/78892113/208623739-3d766675-2ba6-4d05-b1e0-b735a445b544.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
기존에는 관계형 데이터베이스인 RDB만을 많이 이용하였는데 요즘에는 그 외에 다양한 NoSQL 데이터베이스도 이용을 하면서 빅데이터 시대를 준비하고 있습니다.  
이 중에 시계열 데이터를 다루는 데이터베이스, 그 중에서도 가장 많이 이용하는 InfluxDB에 대해 알아보고자 합니다. 
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/208623739-3d766675-2ba6-4d05-b1e0-b735a445b544.png){: .align-center}{: width="70%" height="70%"} 

<br>

# InfluxDB

현재 사용할 수 있는 시계열 데이터베이스 중에서 가장 유명하며 Go 언어로 작성이 되었습니다. Influxdata의 `TICK Stack` 중 하나이며 빠른 처리를 자랑합니다. 그 외에 동시성 및 I/O 처리 성능이 뛰어나며 압축 알고리즘을 적용하여 저장 용량 측면에서도 뛰어납니다. InfluxDB는 2022년 11월 기준 TimeSeries DBMS 중에서 압도적인 점수로 1위를 기록하고 있습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/208624422-666acd6f-f3b7-408c-a8f3-acbc063dc817.png){: .align-center}{: width="80%" height="80%"} 

<br><br>

# 용어

InfluxDB와 RDB에서 사용하는 용어를 비교하면 아래와 같습니다.

<br>

| **RDB**              | **InfluxDB**          |
|----------------------|-----------------------|
| database             | database              |
| table                | measurement           |
| column               | key                   |
| PK or indexed column | tag key (only string) |
| unindexed column     | field key             |
| SET of index entries | series                |

<br>

기존 RDB에서는 table이라고 하는 개념이 InfluxDB에서는 `measurement`라고 합니다. 모든 measurement에는 `time` 키가 자동으로 들어갑니다. Timeseries 데이터베이스라서 그런 것 같습니다.  
column의 경우 `key`라고 하는데 **tag key**와 **field key**가 존재합니다. SQL로 비유하면 tag 키는 WHERE 절에서 사용되는 인덱스 키이고 field key는 나머지라고 보면 됩니다. 

<br><br>

# 설치

이제 InfluxDB를 설치해보겠습니다. OS는 Ubuntu 20.04에서 진행을 하였습니다.  
먼저 InfluxDB를 다운로드합니다.

```sh
wget https://dl.influxdata.com/influxdb/releases/influxdb_1.7.8_amd64.deb
```

![image](https://user-images.githubusercontent.com/78892113/208626302-ca55ea57-0586-4c84-b053-335706b926f1.png){: .align-center}{: width="80%" height="80%"} 

<br>

다운로드가 완료되면 해당 파일을 설치합니다.

```sh
sudo dpkg -i influxdb_1.7.8_amd64.deb
```

![image](https://user-images.githubusercontent.com/78892113/208626441-f2820cfe-3fb2-421b-9dee-acc965b16bb3.png){: .align-center}{: width="80%" height="80%"} 

<br>

설치했다면 서비스에 등록합니다.

```sh
systemctl enable --now influxdb
systemctl status influxdb
```

![image](https://user-images.githubusercontent.com/78892113/208626580-98e5e574-d398-4deb-b6ae-84b614ba4bd2.png){: .align-center}{: width="80%" height="80%"} 

<br>

서비스에 등록하고 상태를 보면 위와 같이 되어있는 것을 보실 수 있습니다.  
그 후에는 InfluxDB를 실행시키고 접속을 해보겠습니다.

```sh
# InfluxDB 실행
influxd

# 쉘 접속
influx
```

![image](https://user-images.githubusercontent.com/78892113/208626761-784160f7-0aea-4afe-ae1d-ff17109f956c.png){: .align-center}{: width="80%" height="80%"} 

위와 같이 쉘이 실행되면 설치 및 접속이 완료되었습니다.

<br><br>

# 설정

접속이 되면 처음에 관리자용 계정을 하나 만듭니다.

```sql
CREATE USER admin WITH PASSWORD 'admin' WITH ALL PRIVILEGES
```

![image](https://user-images.githubusercontent.com/78892113/208627079-523ce931-f3cc-401a-85d6-827d562e0956.png){: .align-center}{: width="80%" height="80%"} 

계정을 `admin`으로 하고 암호도 `admin`으로 설정했습니다.  
다음으로는 InfluxDB 인증을 활성화합니다.

<br>

```sh
vi /etc/influxdb/influxdb.conf
```

![image](https://user-images.githubusercontent.com/78892113/208627276-6a48d9a3-5feb-4c02-9aa4-6cec18791483.png){: .align-center}{: width="80%" height="80%"} 

<br>

```sh
auth-enabled = true
```

![image](https://user-images.githubusercontent.com/78892113/208627369-09ac7b50-30ff-4150-9722-3d591fbaca2c.png){: .align-center}{: width="80%" height="80%"} 

중간에 보면 `[http]` 세션이 있는데 이 부분에 추가합니다.  
InfluxDB 서비스를 다시 시작합니다.

<br>

```sh
systemctl restart influxdb
```

![image](https://user-images.githubusercontent.com/78892113/208627530-0cfe9c66-8a1c-42d6-8e7b-286b3efcc2f8.png){: .align-center}{: width="80%" height="80%"} 

<br>

앞에서 만든 admin 계정으로 접속을 해봅시다.

![image](https://user-images.githubusercontent.com/78892113/208627623-eba1b751-28e2-4960-a2cf-bc03819f85c4.png){: .align-center}{: width="80%" height="80%"} 

제대로 접속되는 것을 확인하실 수 있습니다.