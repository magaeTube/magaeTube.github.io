---
title:  "Airflow 구축 및 변천사 - 도입 배경 및 선정"
date:   2022-06-06 09:00:00 +0900
categories:
  - Data
  - Airflow
tags:
  - Data
  - Airflow
  - Workflow
toc: true
toc_sticky: true
header-img: https://user-images.githubusercontent.com/78892113/172186672-025cb8a1-cddf-4b90-b08c-a8c3634cbb90.png

header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
현재의 회사에서 Airflow를 1년 정도 넘게 사용 중인데 처음 Airflow를 도입하게 된 배경과 그동안의 변천사, 그 과정에서 생긴 고민들에 대한 내용을 정리하고자 합니다.
{: .notice--info}

<br>

관련 글들은 아래 링크로 확인하실 수 있습니다.

* <a href="https://magaetube.github.io/data/airflow/Airflow-Setup-Step-Intro/">Airflow 구축 및 변천사 - 도입 배경 및 선정</a>
* <a href="https://magaetube.github.io/data/airflow/Airflow-Setup-Step-Setup/">Airflow 구축 및 변천사 - 구축</a>
* <a href="https://magaetube.github.io/data/airflow/Airflow-Setup-Step-Improvement/">Airflow 구축 및 변천사 - 개선</a>
* <a href="https://magaetube.github.io/data/airflow/Airflow-Setup-Step-Monitoring/">Airflow 구축 및 변천사 - 모니터링</a>

<br>

![airflow](https://user-images.githubusercontent.com/78892113/172186672-025cb8a1-cddf-4b90-b08c-a8c3634cbb90.png){: .align-center}{: width="80%" height="80%"}

<br><br>

## Airflow 도입 배경

<br>

### 상황

현재의 회사에 처음 입사하였을 때 데이터 업무에 대해 시작하는 초기 단계였기 때문에 많은 것들이 없었습니다. 그중에서 데이터를 수집하고 DW에 올리는 작업 등 주기적인 작업은 배치작업으로 진행하였는데 이때 우분투 서버에서 `크론잡`을 이용하였고 당시에는 약 `5,6개 정도의 프로세스`가 실행되고 있었습니다. 

<br>

### 불편한 점 

해당 프로세스들에 대한 업무를 받고 관리하였는데 아래와 같은 몇 가지 불편한 점이 있었습니다.

* 해당 프로세스 실행의 `성공/실패 여부`를 알 수 없다.
* `데이터의 누수 여부`를 알 수 없다.
* 위의 작업을 매번 `서버에 접속해서 확인`해야 한다. 

<br>

1. **해당 프로세스 실행의 성공/실패 여부를 알 수 없다.**  
&nbsp;회사에서 `파이썬`을 주언어로 사용하여 개발하고 `크론잡`을 이용하여 주기적으로 실행하도록 하였습니다. 그러나 주기적으로 실행되는 파이썬 프로그램이 성공했는지 실패했는지에 대해 알 수가 없었습니다. 정확히 말하자면 <u>알 수가 없는 것은 아니고 매번 확인을 해야 한다는 불편함</u>이 있었습니다. 파이썬 프로그램이 실행될 때 로컬에 로그 파일을 남기도록 개발했기 때문에 성공했는지 실패했는지, 에러가 발생하면 어느 부분에서 문제가 발생하였는지에 대해 로그 파일안에서 일일이 찾아봐야 했습니다.

2. **데이터의 누수 여부를 알 수 없다.**  
&nbsp;위와 같이 로컬에 로그를 남기는 방식으로 하여 파이썬 프로그램이 에러 없이 실행되었는지에 대해서는 확인을 할 수 있었습니다. 그러나 프로그램의 중간 과정에서 <u>데이터의 누수가 있는지를  확인</u>할 수가 없었습니다.  
&nbsp;어느 날 현업 담당자가 본인이 확인하는 데이터의 양이 너무 적다고 요청을 한 적이 있습니다. 해당 프로세스는 데이터를 수집해서 가공하고 그 결과를 DW나 OLAP 데이터베이스에 반영하는 작업이었는데 데이터양이 적다는 것이었습니다.  
&nbsp;당시에는 이를 확인하기 위해서 두 가지로 확인하였습니다.  
&nbsp;첫 번째로는 <u>DW, OLAP 데이터베이스에 접속하여 데이터가 정상적으로 저장되어 있는지 확인</u>하는 방법  
&nbsp;두 번째로는 파이썬 프로그램을 통해 남긴 <u>로컬 로그 파일을 확인하여 문제가 없는지 확인</u>하는 두 가지 방법을 이용하였습니다.  
&nbsp;그러나 해당 작업을 매 배치작업이 진행될 때마다 확인을 할 수는 없는 노릇입니다. (~~초반에는 데이터 확인을 하기 위해서 매번 무식하게 직접 확인하였습니다.~~) 저로서는 굉장히 불편하고 많은 시간을 소요하는 작업이었습니다.

3. **위의 작업을 매번 서버에 접속해서 확인해야 한다.**  
&nbsp;배치 작업이 실행되는 환경은 제 PC가 아닌 `다른 서버에 구축`되어 있었습니다. 위의 1, 2번을 확인하기 위해서는 매번 <u>해당 서버로 접속해서 내용을 확인</u>했습니다. (~~모니터링 시스템을 구축하지 않은 것에 대해 의아할 수는 있지만 당시에는 데이터 업무를 시작하는 단계였기 때문에 빠르게 구축해서 실행하는 것이 주 목표였습니다.~~)

<br><br>

## Airflow 선정 

위와 같은 문제들로 인해 개선이 필요하다고 생각했습니다. 먼저 개선을 하기 위해 필요한 사항들을 파악해보았는데 다음과 같았습니다.

<br>

### 필요 사항

* 손쉬운 프로세스 성공/실패 여부 파악
* 문제가 발생했을 때 원인을 빠르게 파악하고 조치할 수 있는 환경
* 편리한 스케줄 관리 
* 어디서든 편하게 확인할 수 있는 환경

이러한 요구사항들과 함께 배치 스케줄링과 워크플로우 프로그램들을 효율적으로 관리할 수 있는 툴에 대해 조사하게 되었습니다. 

워크플로우로 많이 이용하는 툴에는 `Apache Airflow`, `Apache Oozie`, `Luigi`가 있었습니다. 간단하게 해당 툴들을 살펴보면 다음과 같습니다.

<br>

### 툴 비교

<br>

#### Apache Airflow

![airflow](https://user-images.githubusercontent.com/78892113/172186672-025cb8a1-cddf-4b90-b08c-a8c3634cbb90.png){: .align-center}{: width="80%" height="80%"} 

* 2015년 Airbnb에서 만든 워크플로우 오픈소스
* `Python` 코드로 `DAG`을 구성하여 워크플로우를 정의
* `Web UI`를 통해 DAG, 스케줄링, 커넥터 등을 관리
* 높은 확장성 
* URL : <a href="https://airflow.apache.org/">Apache Airflow</a>, <a href="https://github.com/apache/airflow/">GitHub</a>


<br>

#### Apache Oozie

![image](https://user-images.githubusercontent.com/78892113/172192514-7152f803-6218-480e-a5bd-9f0a80fd83fb.png){: .align-center}{: width="80%" height="80%"} 

* `하둡`의 잡을 관리하기 위한 서버 기반의 워크플로우
* `xml` 코드로 `DAG`을 구성
* `하둡 에코시스템` 특화
* URL : <a href="https://oozie.apache.org/">Oozie</a>


<br>

#### Luigi

![luigi](https://user-images.githubusercontent.com/78892113/172192664-8656eb12-3f74-4b6a-8103-60df4e2d62c7.png){: .align-center}

* Spotify에서 개발한 워크플로우 오픈소스
* `Python`으로 `DAG` 작성 
* URL : <a href="https://github.com/spotify/luigi">GitHub</a>


<br><br>

### 최종 선정

3가지 모두 공통적인 부분은 <u>UI를 제공하여 스케줄링을 모니터링하고 관리하기 쉽다</u>는 점입니다. 그러면 이 중에서 Apache Airflow를 선택한 이유는 다음과 같습니다.  

* 현재 팀에서 ​`파이썬을 주 언어로 이용`하고 있다.
* `하둡 시스템`을 이용하고 있지 않다.
* 가장 많이 이용하고 제공하는 `관련 문서​`가 많다. 

GitHub Stars도 많은 수를 차지하고 있고 많은 기업들이 이용하는지에 대해서는 기업들의 채용공고를 통해 확인하였습니다. (채용 공고를 확인하면 해당 기업의 사용 기술과 요즘 트렌드를 알 수 있습니다.)

위와 같은 이유로 최종적으로 `Airflow`를 선정하고 구축에 들어가기 시작합니다. 