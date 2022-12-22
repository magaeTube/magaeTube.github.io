---
title:  "[Data Engineering] Druid - Superset 연동하기"
date:   2020-11-05 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - Druid
  - Superset
toc: true
toc_sticky: true
toc_label: "Druid 연동하기"
header-img: https://user-images.githubusercontent.com/78892113/209071809-f02c0a94-a658-4671-a563-116f4872a9e1.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
앞에서 설치한 Druid와 Superset을 연동하는 방법에 대해 알아보겠습니다. 앞에서는 virtualenv를 이용해서 Superset을 시작했었는데 중지시키고 진행합니다. 
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209071809-f02c0a94-a658-4671-a563-116f4872a9e1.png){: .align-center}{: width="70%" height="70%"} 

<br>

# Druid 데이터베이스 드라이버 설치 

Superset에서 데이터베이스 connection을 만들기 위해서 드라이버를 설치해야 합니다.  
공식 홈페이지 가이드는 <a href="https://superset.apache.org/docs/databases/installing-database-drivers/">링크</a>에서 확인 가능합니다.

<br>

이를 보면 Druid의 경우 파이썬의 `pydruid` 패키지를 설치해야 합니다. 

```sh
pip install pydruid
```

![image](https://user-images.githubusercontent.com/78892113/209072562-eafbcfcb-5ef0-4f70-982e-dfa11f0281ad.png)

<br>

설치가 완료되면 다시 Superset을 시작합니다.

```sh
superset run -p 8080 --with-threads --reload --debugger
```

![image](https://user-images.githubusercontent.com/78892113/209072827-a45a96fd-4f4e-41d9-8c00-6344ad2646c0.png)

<br><br>

# Druid 데이터베이스 생성

`Superset` 페이지를 실행시킵니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209072888-3d0714e7-9ee1-467f-afef-3d81838bba3c.png)

실행시키면 상단 메뉴 중에 **[Sources] - [Databases]**을 클릭합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209072976-0a77f314-3a74-4890-9f16-f9ea33d4993d.png)

우측 상단의 **'+'** 버튼을 클릭하여 새로운 Database를 연결합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073052-91f5120d-586d-4710-8a9b-bd1d80eee0f5.png)

Database 정보를 입력하는 창이 뜨는데 위의 두 군데를 작성합니다.

* **Database** : 데이터베이스 명
* **SQLAlchemy URI** : `druid://[Druid가 설치된 IP]:[Druid가 설치된 포트]/druid/v2/sql/`

작성을 하고 하단의 `TEST CONNECTION`을 클릭하여 연결 테스트합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073305-37ca7c7f-6ef7-4cdd-abb0-99fd4d7c11bc.png)

위와 같이 성공적으로 연결이 되면 **확인** 버튼을 클릭하고 맨 아래에 **SAVE** 버튼을 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073393-5d590cd3-7845-4e1f-a7ec-3294c471da4f.png)

그러면 위와 같이 우리가 만든 `Druid` 데이터베이스가 생성된 것을 확인하실 수 있습니다.

<br><br>

# 시각화 만들어보기

이번에는 연결된 Druid 데이터베이스에 있는 정보를 가져와서 간단한 시각화를 만들어보겠습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073522-17909780-3fb0-468e-85e0-9eb59258adcb.png)

상단 메뉴에 있는 **[Sources] - [Tables]**를 눌러 메뉴로 이동합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073548-9ee386e4-ad2c-46a0-b547-fc6d6ebdd584.png)

우측 상단에 있는 **'+'** 버튼을 클릭해서 테이블을 추가합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073560-da1de0ee-78b5-41f0-aff7-b6eec5a78be4.png)

테이블을 정의해서 추가할 수 있는데 `Database`에는 위에서 추가한 `druid`를 선택하고 `Table Name`에는 원하는 테이블명을 작성합니다. 이전 예제에서 `wikipedia`라고 만든 datasource가 있기 때문에 해당 datasource를 불러오겠습니다. 작성하고 **SAVE** 버튼을 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073569-00ddc2a5-78c5-4136-a0cf-9a14a5b58f52.png)

그러면 새로운 테이블이 하나 추가된 것을 보실 수 있습니다.  
이 `wikipedia` 를 눌러서 시각화를 하나 만들겠습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073579-9db9b91a-8b93-483f-a382-f0f0f8fbf443.png)

`wikipedia`를 누르면 위와 같이 나옵니다.  
그냥 제일 평범하게 데이터들을 테이블 형태로 보여주는 것을 해보겠습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073586-1d02a0d4-7f42-459c-96b9-5d5b810e9c16.png)

좌측 메뉴에 **[Query]** 부분에서 `Query Mode`를 `RAW RECORDS`로 변경합니다. 그러면 아래에 `Columns`를 선택할 수 있는데 여기서는 `channel`, `user`, `countryIsoCode`, `countryName` 4개를 추가해보았습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073594-df012e0a-6e76-44c7-9357-d66cdad1eb36.png)

그 위에 `Time Range`에는 클릭하여 `No filter`로 변경합니다. 변경한 후에는 `RUN QUERY`로 실행시킵니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073601-ea80d7f1-4824-439a-897e-92f62e6338c0.png)

그러면 위와 같이 결과가 나오는 것을 확인하실 수 있습니다.  
이제 좌측 상단에 **SAVE** 버튼을 클릭하여 저장하도록 하겠습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209073608-39a9dd92-99c5-4190-97a0-45fdd3ad6010.png)

`Save as`에는 Chart명을 입력하고 아래에는 dashboard에 해당 Chart를 추가할지 묻는 것입니다. 추가하고자 하면 해당 부분에 dashboard를 선택하고 하단의 **SAVE** 버튼을 클릭합니다.

<br><br>

위와 같은 방법을 이용해서 Superset에 Druid 데이터를 연결해서 시각화까지 진행해보았습니다.
