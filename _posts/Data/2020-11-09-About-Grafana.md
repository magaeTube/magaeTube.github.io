---
title:  "[BI] Grafana 소개"
date:   2020-11-09 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - Visualization
toc: true
toc_sticky: true
toc_label: "Grafana"
header-img: https://user-images.githubusercontent.com/78892113/208305323-92671383-d6eb-455c-afeb-c4418d94e485.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
시각화를 위한 툴은 여러 가지가 있는데 시계열 데이터에 대한 시각화를 하기 좋은 Visualization Tool인 Grafana에 대해 알아보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/208305323-92671383-d6eb-455c-afeb-c4418d94e485.png){: .align-center}{: width="50%" height="50%"} 

<br>

# Grafana란

`Grafana`란 시계열 매트릭 데이터를 시각화하는데 최적화된 오픈소스 Visualization Tool입니다. 다양한 DB를 연결하여 데이터를 가져와서 시각화할 수 있으며 간단한 클릭으로 완료할 수 있습니다. <a href="https://play.grafana.org/d/000000012/grafana-play-home?orgId=1">링크</a>를 통해 체험해볼 수 있습니다.

<br>

`Grafana`는 서버 리소스의 매트릭 정보나 로그 같은 데이터를 시각화할 때도 많이 사용합니다. 알람 기능도 제공하여 특정 수치 이상으로 값이 치솟았을 때 알람을 전달받을 수 있습니다.

<br><br>

# 설치

설치는 `Ubuntu 20.04` 환경에서 진행하였습니다.

<br>

## Repository 추가

```sh
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
```

![image](https://user-images.githubusercontent.com/78892113/208305844-7dd6961b-1d98-4f78-a47b-5ba32ec70362.png){: .align-center}{: width="50%" height="50%"} 

이 과정에서 중간에 **공개키가 없기 때문에 인증할 수 없다**라고 뜨면 이를 해결해야 합니다. 해결 방법에 대해서는 <a href="https://sarc.io/index.php/forum/tips/3037-gpg-error-http-ppa-launchpad-net-inrelease-the-following-signatures-couldn-t-be-verified-because-the-public-key-is-not">링크</a>를 통해 확인해봅니다.  
위의 내용을 해결한 후에 다시 등록합니다.

<br>

## 키 다운로드

```sh
curl https://packages.grafana.com/gpg.key | sudo apt-key add -
```

![image](https://user-images.githubusercontent.com/78892113/208306046-3ce3305b-1937-41e5-aae9-bc39cc4bef72.png){: .align-center}{: width="50%" height="50%"} 

<br>

## Grafana 설치

```sh
sudo apt-get install grafana
```

Ubuntu 환경이기에 `apt-get`으로 Grafana를 설치합니다.

<br>

## 서비스 등록 및 시작

설치가 완료되면 서비스에 등록하여 실행하도록 합니다.

```sh
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

<br>

## 접속
<a href="http://localhost:3000/login">http://localhost:3000/login</a>으로 접속하여 확인합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/208306206-0c486405-426d-4bda-8fb5-e2b0aa90b4cd.png){: .align-center}

기본 username과 Password는 admin/admin인데 접속하면 변경을 하라고 합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/208306260-d3956523-5813-4548-90d8-01f76afc6ea1.png){: .align-center}

변경 작업을 하고 들어가면 위와 같이 정상적으로 나오는 것을 확인하실 수 있습니다.