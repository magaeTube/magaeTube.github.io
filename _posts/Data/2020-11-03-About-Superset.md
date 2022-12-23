---
title:  "[BI] Apache Superset은.."
date:   2020-11-03 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - Superset
header-img: https://user-images.githubusercontent.com/78892113/209071809-f02c0a94-a658-4671-a563-116f4872a9e1.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
BI 툴에는 여러 가지가 존재하는데 그 중 하나인 아파치 재단에서 제공하는 Apache Superset에 대해 알아봅니다. 
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209071809-f02c0a94-a658-4671-a563-116f4872a9e1.png){: .align-center}{: width="70%" height="70%"} 

<br><br>


<a href="https://superset.apache.org/">Apache Superset</a>은 웹에서 제공하는 오픈소스 `BI(Business Intelligence)` 툴입니다.  
여기서 `BI(Business Intelligence)`는 기업에서 데이터를 수집, 정리, 분석하고 활용하여 효율적인 의사결정을 할 수 있는 방법에 대한 학문입니다. (위키백과 참고)  
즉, 기업에서 의사결정을 하기 위해서 여러 곳에 흩어져있는 데이터들을 한곳으로 수집하고 정리/분석하고 효과적인 시각화 방법을 이용해서 의사결정을 도와주는 것을 말합니다. 기존에 단순히 데이터들을 일반적인 텍스트로 나열하는 방식이 아니라 차트나 테이블, 지도 등을 통해서 보기 쉽고 판단하기 편하게 해줍니다. 

<br>

`Superset`은 몇 가지 특징을 가지고 있습니다. 
* 단순 클릭을 이용한 **다양한 시각화 제공** (기본적인 Line, Bar, Pie 차트부터 해서 여러 Table과 3D 등 40여 개 제공)
* 이를 이용한 **대시보드** 구현 가능
* 개별적으로 데이터 셋에 접근할 수 있는 여러 룰을 제공하는 확장 가능한 **보안/접근 방식** 
* SQLAlchemy를 통해 RDBMS와 SQL 이용
* Druid.io와 깊은 연관성

아래는 구글에서 가져온 Superset 예시입니다.

![image](https://user-images.githubusercontent.com/78892113/209308996-53765995-ab8a-4246-8f43-c8888b61732e.png)