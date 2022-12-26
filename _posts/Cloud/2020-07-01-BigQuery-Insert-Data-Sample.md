---
title:  "[BigQuery] 구글 빅쿼리 (3) - 데이터 이용해보기 (with Kaggle)"
date:   2020-07-01 00:00:00 +0900
categories:
  - Cloud
tags:
  - Cloud
  - GCP
  - BigQuery
toc: true
toc_sticky: true
toc_label: "Kaggle 데이터 이용하기"
header-img: https://user-images.githubusercontent.com/78892113/209471556-8c6c325a-8c24-48a0-a12c-4f9ffea0bbc3.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
BigQuery 프로젝트까지 생성되었다면 이제 실제 데이터를 이용해서 BigQuery에 데이터를 넣고 검색하는 과정을 보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209471556-8c6c325a-8c24-48a0-a12c-4f9ffea0bbc3.png){: .align-center}{: width="70%" height="70%"} 

<br>

# 데이터 다운로드

데이터는 캐글에서 제공해 주는 Covid-19 코로나 바이러스 데이터를 이용해서 해보겠습니다. <a href="https://www.kaggle.com/datasets/imdevskp/corona-virus-report">다운로드</a>  
가장 최근에 올라온 데이터이자 용량도 적당한 용량(58.92MB)를 제공하는 데이터를 이용하겠습니다. 위의 다운로드 페이지로 이동하면 아래와 같습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209525166-1c0a7eb5-61c6-417b-a08a-17b63c51ad45.png){: .align-center}

해당 페이지에서 우측 상단에 **`Download`**를 눌러 자료를 다운로드합니다. 다운로드를 누르면 로그인하라고 나오니 로그인 또는 회원가입하고 이용해 주세요. 로그인을 하고 다운로드를 진행하면 됩니다. 

<br><br>

# 데이터 파악

캐글에서는 아래로 내려보면 해당 데이터 셋에 대한 정보를 다음과 같이 제공합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209525169-167936e9-d64d-456b-9925-874fd8833cc0.png){: .align-center}

**`Data Explorer`**에서는 데이터 종류가 있습니다. 해당 데이터 셋에서는 `country_wise_latest.csv`를 포함하여 총 6개의 csv 파일이 존재하네요. 해당 csv들을 클릭해보면 우측에 데이터에 대한 정보가 나옵니다. 원하는 데이터(csv)에 대한 정보를 보고 싶을 때는 해당 csv를 눌러 확인하도록 합니다. 

<br><br>

# BigQuery 데이터셋 생성

캐글데이터(.zip파일)을 모두 다운로드하였으면 압축을 풀고 해당 데이터를 **BigQuery에 업로드**해보겠습니다. GCP에서 BigQuery로 넘어오면 다음과 같은 화면을 볼 수 있습니다. (여러분의 화면은 저랑 조금씩 다를 수가 있어요..)

<br>

![image](https://user-images.githubusercontent.com/78892113/209525177-bdb9c5e5-151d-4019-ba94-fcb351748967.png){: .align-center}

해당 화면 좌측에 bigquerytest-282004 (빨간색 박스, 여러분은 다른 이름일 겁니다.)를 클릭합니다. 클릭하면 화면이 달라지는데 여기에 다운로드한 csv 파일들을 올려볼 겁니다.  
화면의 우측 중앙에 **`데이터세트 만들기`**를 클릭합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209525187-4a0f4949-5794-4dd8-bdd9-212905b97da9.png){: .align-center}

**`데이터세트 만들기`**창이 나오면 아래와 같이 데이터를 입력합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209525196-c8519ea9-4a6f-4a9f-aa47-acc835713b3f.png){: .align-center}{: width="70%" height="70%"}

* **데이터세트 ID (이름)** : covid19
* **데이터 위치 (GCP 리전)** : 서울(asia-northeast3)

해당 형태로 입력하고 **`데이터세트 만들기`**를 클릭합니다. 이렇게 클릭하면 오류가 발생하면서 데이터세트가 생성되지 않는데 오류 메시지를 보면 아직 서울 리전에서는 BigQuery를 제공하지 않나 봅니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209525200-464c1535-03fd-4f87-aee8-b1ae08af4ac7.png){: .align-center}

이러면 이제 데이터 위치를 **미국(US)**로 변경해서 다시 만들겠습니다.
데이터세트 생성이 완료되면 좌측에 `bigquerytest-202004` 하단에 `covid19` 데이터세트가 생성된 것을 확인할 수 있습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209525209-482fa5cc-88cc-4822-a786-120658965265.png){: .align-center}

<br><br>

# BigQuery 데이터 업로드

데이터 세트가 생성되면 해당 covid19 데이터 세트를 클릭합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209525218-87d918b9-8c10-4729-8a20-ba72ce52ced1.png){: .align-center}

여기서 **`테이블 만들기`**를 클릭하여 우리가 다운로드한 csv 파일들을 올릴 것입니다. csv 파일 하나당 테이블 하나라고 생각하시면 됩니다. **`테이블 만들기`**를 클릭합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209525224-9c1d021d-85c4-4204-8461-492c895d12d6.png){: .align-center}

그리고 다음과 같이 데이터들을 입력합니다. 

**소스**

* 다음 항목으로 테이블 만들기 : 업로드
* 파일 선택 : csv 파일 선택
* 파일 형식 : CSV

<br>

**대상**

* 테이블 이름 : CSV 파일과 동일하게 해주세요

<br>

**스키마**

* 테이블 스키마(칼럼명, 자료형)을 지정하는 부분입니다.
* 아래 각 csv 파일에 맞는 스키마를 복사해서 넣어주세요 (usa_county_wise.csv는 10MB가 넘기 때문에 생략합니다.)
* `텍스트로 편집` 체크

```
# country_wise_latest.csv
country_region:string,confirmed:integer,deaths:integer,recovered:integer,active:integer,new_cases:integer,new_deaths:integer,new_recovered:integer,deaths_100_cases:float,recovered_100_cases:float,deaths_100_recovered:string,confirmed_last_week:integer,one_week_change:integer,one_week_percent_increase:float,who_region:string

# covid_19_clean_complete.csv
province_state:string,country_region:string,lat:float,long:float,cum_date:date,confirmed:integer,deaths:integer,recovered:integer,active:integer,who_region:string

# day_wise.csv
wise_date:date,confirmed:integer,deaths:integer,recovered:integer,active:integer,new_cases:integer,new_deaths:integer,new_recovered:integer,deaths_100_cases:float,recovered_100_cases:float,deaths_100_recovered:float,no_of_countries:integer

# full_grouped.csv
grouped_date:date,country_region:string,confirmed:integer,deaths:integer,recovered:integer,active:integer,new_cases:integer,new_deaths:integer,new_recovered:integer,who_region:string

# worldometer_date.csv
country_region:string,continent:string,population:integer,total_cases:integer,new_cases:integer,total_deaths:integer,new_deaths:integer,total_recovered:integer,new_recovered:integer,active_cases:integer,serious_critical:integer,tot_cases_1m_pop:integer,deaths_1m_pop:integer,total_tests:integer,tests_1m_pop:integer,who_region:string
```

<br>

**고급 옵션**

건널뛸 헤더 행 : 1  
세팅을 하고 **`테이블 만들기`**를 클릭하여 테이블을 만듭니다. 이렇게 6개의 csv 파일들을 모두 생성해 줍니다. 근데 로컬 업로드는 10MB로 제한되어 있기 때문에 'usa_county_wise.csv'는 생략하고 5개로 하겠습니다.  
5개를 전부 넣으면 아래와 같이 테이블들이 생성된 것을 확인할 수 있습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209525235-e7ee63ef-02a7-4f22-84d0-2ef99ee10993.png){: .align-center}

<br><br>

# 쿼리 확인

이제 이를 이용해서 SQL을 해보고 데이터가 들어갔는지 확인해보겠습니다.나라별 데이터(country_wise_latest)에서 대한민국의 상황을 보도록 하겠습니다. 대한민국에서 확진자 수, 사망자 수, 격리 해제 수를 나타냅니다 

<br>

```sql
SELECT country_region,
       confirmed,
       deaths,
       recovered,
       active
  FROM `covid19.country_wise_latest`
 WHERE country_region='South Korea'
```

쿼리 편집기에 해당 쿼리를 작성하고 **`실행`**버튼을 클릭하면 아래와 같이 결과가 잘 나온 것을 확인할 수 있습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209525245-1218490b-90e5-4fee-88cf-6ad0b612c2e7.png){: .align-center}

데이터 세트를 생성하고 테이블을 만든 후 간단한 쿼리를 실행시키는 것까지 해보았습니다. 이후에는 좀 더 복잡한 것으로 진행해보겠습니다. 