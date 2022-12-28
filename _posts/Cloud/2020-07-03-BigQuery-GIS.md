---
title:  "[BigQuery] 구글 빅쿼리 - GIS로 지리 정보 이용하기"
date:   2020-07-03 00:00:00 +0900
categories:
  - Cloud
tags:
  - Cloud
  - GCP
  - BigQuery
toc: true
toc_sticky: true
toc_label: "GIS 이용하기"
header-img: https://user-images.githubusercontent.com/78892113/209471556-8c6c325a-8c24-48a0-a12c-4f9ffea0bbc3.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
BigQuery는 GIS 기능을 제공하여 효과적인 시각화를 제공한다고 했습니다. 이번에는 GIS를 이용하여 시각화를 해보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209471556-8c6c325a-8c24-48a0-a12c-4f9ffea0bbc3.png){: .align-center}{: width="70%" height="70%"} 

<br>

진행 순서는 다음과 같이 진행합니다.

1. 예제에 맞는 **`Query`** 작성
2. BigQuery **`Geo Viz`**에서 결과 시각화

<br>

데이터는 이전에 이용하였던 `covid19` 데이터를 이용하겠습니다. GIS 데이터를 이용하기 위해서는 `위도`와 `경도`가 있는 데이터를 이용하려고 합니다. 이전에 등록해놓은 테이블들 중에 **`covid_19_clean_complete`** 테이블을 확인해보면 **`위도(lat)`**, **`경도(long)`** 데이터가 있는 것을 보실 수 있습니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209806744-b0ea6254-3686-44f1-b8fd-0e61873bf968.png){: .align-center}

이외에도 해당 테이블에는 지역, 국가, 누적 날짜, 확진자 수 등 여러 데이터가 있는데 **2020년 6월 23일에 유럽 30개 국가의 확진자 수**를 살펴보는 예제로 해보겠습니다.

<br><br>

# 예제에 맞는 Query 작성

해당 예제에 맞게 결과가 나오도록 먼저 쿼리를 짜야 하는데요. 쿼리를 작성할 때는 **`ANSI-SQL`** 방식으로 작성을 하면 됩니다. 하나 추가해야 하는 것이 지도에 표시하기 위해서 위도와 경도를 이용하고 `GEOGRAPHY` 형태로 변환시키는 작업이 필요합니다. 이럴 때 사용되는 함수가 **`ST_GeogPoint(경도, 위도)`**입니다. 그러면 해당 내용을 가지고 쿼리를 작성합니다. (데이터 수가 많아서 30개로만 제한을 걸었습니다.)

<br>

```sql
SELECT country_region,
       ST_GeogPoint(long, lat) as WKT,
       confirmed
  FROM covid19.covid_19_clean_complete
 WHERE cum_date='2020-06-23'
   AND who_region='Europe'
 LIMIT 30 
```

작성을 하고 쿼리를 실행하면 아래와 같이 데이터가 나오는 것을 보실 수 있습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209806765-5e8807ac-1681-44d9-a5e7-b0dfde8a7649.png){: .align-center}

<br><br>

# BigQuery Geo Viz에서 결과 시각화 

**`BigQuery Geo Viz`**는 Google Maps API를 이용하여 BigQuery에서 지리 공간 데이터를 시각화하는 웹 도구를 말합니다. 해당 도구를 이용하기 위해서는 데이터에 대한 액세스 권한을 먼저 인증하고 부여해야 합니다. 

<br>

## Geo Viz 이동 및 데이터 허용

<a href="https://bigquerygeoviz.appspot.com/?hl=ko">URL</a>을 통해 페이지를 이동하면 다음과 같은 페이지가 나오게 됩니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209806779-259eb23a-324a-489e-8c2e-c7e0c6491084.png){: .align-center}

여기서 좌측 1번 **Query**에 **`Authorize`**를 클릭합니다그러면 로그인하라는 새로운 창이 뜨고 로그인을 진행합니다. 로그인하고 액세스 문구에서 **허용**을 눌러 처리합니다. 

<br>

## Query 작성

![image](https://user-images.githubusercontent.com/78892113/209806790-bf03c7e6-2969-4690-b141-0fb13337ec8b.png){: .align-center}

허용을 누르면 원래의 화면이 바뀌고 **프로젝트 ID**를 정해야 합니다. 저희가 BigQuery에서 만든 ID (저의 경우 bigquerytest-282004)를 선택하고 아래 칸에는 아까 만들었던 `Query`를 작성하고 **Run**을 클릭합니다. Run 해서 결과가 나오면 옆에 **Show results**버튼을 클릭합니다. 

<br>

## 데이터 확인, 위치 설정

![image](https://user-images.githubusercontent.com/78892113/209806800-cb4e37e7-97d6-489e-97b0-90b4a389c52f.png){: .align-center}

2번 **`Data`**로 넘어오면 **`Geometry column`**에서 **WKT**를 선택하고 **Add styles**를 클릭합니다. 여기서 styles은 어떠한 방식으로 표현할지에 대한 내용입니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209806815-2d997e7a-cd65-45d5-8d7e-3fd5906329be.png){: .align-center}

* **fillColor** : 다각형 또는 점의 채우기 색상
* **fillOpacity** : 다각형 또는 점의 채우기 불투명도. 값은 0(투명) ~ 1(불투명)
* **strokeColor** : 다각형 또는 선의 획이나 윤곽선 색상
* **strokeOpacity** : 다각형 또는 선의 획이나 윤곽선 불투명도. 값은 0~1
* **stokreWeight** : 다각형 또는 선의 획 또는 윤곽선 너비. 픽셀 단위
* **circleRadius** : 점을 나타내는 원의 반지름. 픽셀 단위

현재 지도에 점이 매우 작지만 찍혀있긴 합니다. 이를 키워서 좀 보기 쉽게 만들어보겠습니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209806865-61ab0148-0e4b-4d31-a1f1-989d23349c73.png){: .align-center}

circleRadius 오른쪽에 화살표를 누르고 **Data-driven**을 클릭하고 위와 같이 작성을 합니다. 

* **Function** : linear
* **Field** : confirmed 
* **Domain** : 1 ~ max 값
* **Range** : 1 300000

해당 값을 입력하고 **Apply Style**을 클릭하면 아래와 같이 결과가 나타난 것을 보실 수 있습니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209806874-d3d6295f-6d36-4d62-bf06-8d45c7d8cabb.png){: .align-center}

해당 지도로만 봤을 때는 유럽 국가 중에 확진자는 스페인, 프랑스, 이탈리아, 터키 4개 나라가 압도적으로 많은 것을 보실 수 있습니다.  
지금까지 BigQuery에 있는 GIS 기능을 간단하게 이용해보았습니다. 