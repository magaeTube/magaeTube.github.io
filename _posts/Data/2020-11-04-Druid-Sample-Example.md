---
title:  "[Data Engineering] Apache Druid 예제 따라하기"
date:   2020-11-04 00:00:00 +0900
categories:
  - Data
  - Druid
tags:
  - Data
  - Druid
toc: true
toc_sticky: true
toc_label: "Druid 예제"
header-img: https://user-images.githubusercontent.com/78892113/209270038-0e690c31-c629-404d-9a86-38fe361cb57a.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
설치가 완료되었다면 공식 홈페이지에서 제공하는 기본 예제를 따라해보겠습니다. 
{: .notice--info}

<br>

![Druid](https://user-images.githubusercontent.com/78892113/209270038-0e690c31-c629-404d-9a86-38fe361cb57a.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# Data 올리기

Docker 버전이 아니라 Druid를 다운로드해서 설치하면 **apache-druid-0.20.0** 디렉터리가 있습니다. (버전은 다를 수 있습니다.) 해당 디렉터리에 `quickstart/tutorial` 디렉터리로 가면 **wikiticker-2015-09-12-sampled.json.gz** 이름을 가진 파일이 있는 것을 확인하실 수 있습니다. 이를 콘솔에서 올려보겠습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269436-f5dba75f-f636-4e9e-9eee-75ecd8eb320a.png)

콘솔로 접속해서 좌측 상단에 있는 **Load data**를 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269442-6015a040-e5ea-41fd-975f-2503e526c41d.png)

그러면 나오는 화면에서 좌측 **Start a new spec**을 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269451-132e2d68-7b93-4250-b78a-fa79ef4523da.png)

위와 같이 `Kafka`, `Kinesis`, `S3` 등 다양하게 데이터를 올릴 수 있는데 예제는 로컬 데이터이기 때문에 **Local disk**를 클릭하고 우측에 **Connect data**를 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269459-90a9ef99-71bd-4aa9-99cb-2ade2c517272.png)

그러면 위와 같이 나오는데 오른쪽 사이드바 (빨간색 박스)에 내용을 작성합니다.

* **Source type** : local
* **Base directory** : `[tutorial이 있는 경로]`
* **File filter** : wikiticker-2015-09-12-sampled.json.gz

작성하고 **Apply**를 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269468-16202cf5-330d-4926-b138-1f427078291e.png)

위와 같이 raw 데이터가 나온 것을 확인할 수 있습니다. 다음으로 우측 하단에 **Next: Parse data**를 클릭합니다.

<br><br>

# Data 파싱

**Next: Parse data**를 클릭하면 자동으로 파싱이 진행됩니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269482-85fda6aa-8a9f-49e1-9f45-973eea1fb4fb.png)

예제의 데이터가 `JSON` 파일이기 때문에 `JSON` 형태에 맞춰서 자동으로 column과 해당 값이 생성됩니다. 데이터가 `JSON` 파일이 아니면 우측에 `Input format`을 변경하고 **Apply** 버튼을 클릭하여 적용해줍니다. 데이터가 이상 없이 잘 나오면 우측 하단에 **Next: Parse time**을 클릭합니다.

<br><br>

# Time 파싱

이번에는 시간 데이터를 파싱하는 부분입니다. 이전에 설명드렸던 것처럼 `Druid`는 시계열 데이터를 이용하기 때문에 시간 데이터를 설정해줘야 합니다. **Next: Parse time**을 눌러 결과를 봅시다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269487-76369b54-e552-47ba-bf93-07fee7a6cade.png)

이전과 달라진 점은 데이터가 나타난 곳에 맨 왼쪽에 `__time` 칼럼이 생성된 것을 확인할 수 있습니다. 그리고 아래에 `from: 'time'`이라고 쓰여있는 것을 볼 수 있는데 이는 기존 데이터 중에 'time' 칼럼을 `__time` 칼럼으로 시간 데이터를 이용하겠다는 뜻입니다. 자동으로 파싱이 되었지만 변경을 하고 싶다면 우측에 **Column**과 **Format**을 변경하고 **Apply** 버튼을 클릭합니다. 

모두 완료되면 우측 하단에 **Next: Transform**을 클릭합니다. 

<br><br>

# Transform

<br>

![image](https://user-images.githubusercontent.com/78892113/209269491-78edf9a1-1290-400a-9300-fe77b2122904.png)

`Transform` 작업을 할 것이 있다면 진행합니다. 예제에서는 없기 때문에 우측 하단에 **Next: Filter**를 클릭합니다.

<br><br>

# Filter

<br>

![image](https://user-images.githubusercontent.com/78892113/209269497-5e926c62-b263-49bd-8fe4-cad9708655eb.png)

Filter 단계에서도 작업할 것이 있으면 진행하지만 해당 예제에서는 생략하고 **Next: Configure schema**를 클릭합니다.

<br><br>

# Configure schema

각 칼럼에 대해 `type`을 지정하는 단계입니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269519-5b95e70f-095d-4902-859e-b60f2634ed53.png)

Druid에서는 칼럼에 대한 `type`을 지정해 줘야 하는데 위와 같이 각 칼럼을 보고 자동으로 `type`을 지정해 줍니다. 각 칼럼에 모두 지정이 되었는지 확인하고 우측 하단에 **Next: Partition**을 클릭합니다. 

<br><br>

# Partition

데이터를 어떠한 기준으로 나눌 것인지 처리하는 부분입니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269531-8814ffc8-d164-4c44-bf23-d5ca2c5b767c.png)

여기에서는 `Segment`라는 개념이 나오는데 `Segment`는 해당 Partition 작업을 거쳐 나오는 블록(?)이라고 보면 될 것 같습니다. 해당 `Segment granularity`를 **DAY**로 설정하면 시간 데이터가 날짜 기준으로 Segment가 나눠집니다. 우측 하단 **Next: Tune**을 클릭합니다.

<br><br>

# Tune

<br>

![image](https://user-images.githubusercontent.com/78892113/209269540-7315847c-d535-4dee-85ce-3f6a847032ab.png)

튜닝을 하는 부분인데 해당 부분은 생략하고 **Next: Publish**를 클릭합니다.

<br><br>

# Publish

퍼블리싱을 하는 작업입니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269546-0751c29e-06c4-4f4e-9c56-503f817f9a06.png)

좌측에 `Datasource name`을 `wikipedia`로 변경하고 **Next: Edit spec**을 클릭합니다. 

<br><br>

# Edit

마지막으로 확인하는 부분입니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209269552-07ad21c6-6c0d-4d91-ac17-ed68ac6e2e8c.png)

해당 부분이 문제가 없다면 우측 하단 **Submit** 버튼을 클릭합니다.

<br><br>

# 확인

<br>

![image](https://user-images.githubusercontent.com/78892113/209269568-df8e5c10-a9db-4ea4-a42f-ce7db84f026d.png)

완료되면 `Ingestion` 뷰에 `Tasks`에 생성된 것을 확인하실 수 있습니다. 

<br><br>

# Query 실행

이렇게 만들어진 데이터를 Query를 통해 결과를 확인해보겠습니다. 

​<br>

![image](https://user-images.githubusercontent.com/78892113/209269583-23123909-b649-4714-9125-f666366e4fe0.png)

상단 메뉴에 `Datasources`를 클릭하면 우리가 만들었던 **wikipedia** Datasource가 있는 것을 확인할 수 있습니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209269609-91ddffef-39b4-4156-822a-92cda83eeb7b.png)

우측으로 넘어가면 `Actions` 부분에 드라이버 모양을 클릭하고 **Query with SQL**을 클릭합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209269619-54df924e-4588-46f6-9281-970c27fcc18b.png)

그러면 Query창으로 넘어오면서 데이터가 나오는 것을 확인하실 수 있습니다.