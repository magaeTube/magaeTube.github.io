---
title:  "[RDS] CloudWatch를 이용하여 모니터링하기"
date:   2022-08-23 00:00:00 +0900
categories:
  - Cloud
tags:
  - AWS
  - CloudWatch
  - RDS

header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
이번에는 AWS의 CloudWatch를 이용하여 RDS를 모니터링하는 방법에 대해 알아보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/185961381-e4b854a0-a931-4488-8f13-c4c09cee774e.png){: .align-center}{: width="60%" height="60%"} 

<br>

먼저 AWS의 `CloudWatch` 서비스로 이동해보겠습니다.

![image](https://user-images.githubusercontent.com/78892113/185961693-0a09a225-e566-4791-8379-a01430668579.png){: .align-center}

<br><br>

# Metrics 생성

처음으로는 필요한 서비스의 `Metrics`를 생성하도록 합니다. CloudWatch 서비스 좌측에 있는 메뉴에서 <u>[Metrics] - [All metrics]</u>로 이동합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/185962397-8daa791a-ca58-4bbf-9737-4b16a76f540f.png){: .align-center}

그러면 이용중인 여러 서비스들이 나오는데 여기서 RDS를 모니터링할 것이기 때문에 `RDS`를 선택합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/185962528-2b61853b-faad-4b84-915b-a3218c8da558.png){: .align-center}

​그러면 Cluster별로 할것인지 데이터베이스 클래스별로 할 것인지 선택을 할 수 있는데 특정 데이터베이스에 대해서 Metrics가 필요하기 때문에 `[Per-Database Metrics]`를 선택하겠습니다.

<br>

​![image](https://user-images.githubusercontent.com/78892113/185962654-6b892fd3-6266-438f-acf1-a4bfe9d19ee5.png){: .align-center}{: width="60%" height="80%"} 

DB Instance별로 Metric 항목들이 나오는데 내가 모니터링이 필요한 Instance을 찾아 선택하고 `Add to search`를 클릭합니다. 그러면 해당 Instance에 대한 <u>Metric 항목들을 보실 수 있습니다.</u>

<br>

![image](https://user-images.githubusercontent.com/78892113/185962778-0b789bed-789d-48a5-bb7f-0ab2f27d0f17.png){: .align-center}

보고자 하는 `Metric` 항목이 있다면 해당 항목을 선택하면 위의 그래프에 나타나는 것을 확인하실 수 있습니다.

<br><br>

# 대시보드 생성

이렇게 그래프를 만들었다면 이를 대시보드에 추가하겠습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/185962910-7a292dd6-d348-4d0e-9d2b-e02b4ba68491.png){: .align-center}

그래프의 우측 상단을 보시면 `Actions`가 있습니다. 이를 선택하면 `Add to dashboard`가 나오는데 이것을 클릭합니다.

<br>

​![image](https://user-images.githubusercontent.com/78892113/185962978-e952230f-9777-4e3f-b6c9-a58747cbeb40.png){: .align-center}

dashboard 정보를 입력하라고 나오면 정보에 맞게 작성하고 `Add to dashboard` 버튼을 클릭합니다. 새로 만드는 경우에는 `Create new`를 선택해서 새로운 대시보드를 생성합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/185963062-68bef70d-3a3d-43ec-b47c-31d20c5a710c.png){: .align-center}

대시보드가 생성이 되면 우측 상단에 `Save dashboard` 버튼을 클릭해서 마무리합니다.

<br>

​![image](https://user-images.githubusercontent.com/78892113/185963157-530df5c4-4957-42c6-980f-9b8a2bccef29.png){: .align-center}
