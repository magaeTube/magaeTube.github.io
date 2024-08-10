---
title:  "[AWS 자격증 도전기] Certified Data Engineer Associate - 1. 소개 "
date:   2024-07-22 00:00:00 +0900
categories:
  - Data
  - DataEngineering
tags:
  - Data
  - AWS
toc: true
toc_sticky: true
toc_label: "AWS DE 자격증"
header-img: https://github.com/user-attachments/assets/8d898a2b-8562-4907-a0a5-304207a69c6d
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;AWS에서 존재하는 많은 자격증 중에 하나인 Data Engineer Associate가 올해 3월부터 시행되었습니다. 이 자격증을 취득하기 위한 도전기를 작성합니다.
{: .notice--info}

<br>

![AWS](https://github.com/user-attachments/assets/8d898a2b-8562-4907-a0a5-304207a69c6d){: .align-center}{: width="60%"}

<br><br>

&nbsp;회사를 이직하면서 데이터 관련해서 전반적으로 전체를 다루기는 힘들어지고 특정 영역에만 몰두하게 되는 경우가 생깁니다. 그래서 다른 영역에 대해서는 지식이 부족하게 되고 도태될까봐 전체적인 지식을 계속 쌓기 위한 방법을 찾고 있었습니다. 그중에 하나가 자격증을 취득하는 것이고 마침 AWS에서 Data Engineer 관련 자격증이 시행된 것을 알게 되어 이 자격증을 취득하기로 했습니다.

<br>

## 소개
&nbsp;정식 명칭은 `AWS Certified Data Engineer - Associate`로 데이터 관련 AWS 서비스에 대한 역량과 지식, 데이터 수집 및 변환, 프로그래밍 개념을 적용한 데이터 파이프라인 오케스트레이션, 데이터 모델 설계, 데이터 수명 주기 관리, 데이터 품질 보장 능력을 검증하는 자격증입니다.  

&nbsp;그외에 주요한 내용은 다음과 같습니다.

|항목|내용|
|---|----|
|범주|Associate|
|시험 시간|130분|
|시험 형식|65개 문항, 객관식 또는 복수 응답|
|비용|150 USD|
|대면 또는 온라인 테스트|Pearson VUE 테스트 센터 또는 온라인 감독 시험|
|제공 언어|영어, 일본어, 한국어, 중국어 간체|

&nbsp;시험은 모두 객관식으로 65개의 문항을 130분안에 풀어야 합니다. 시험은 온라인, 오프라인 모두 가능하기 때문에 본인에게 맞는 것으로 시험보면 됩니다.

<br>

## 시험 일정 예약
&nbsp;시험의 경우 미리 예약을 통해 진행하실 수 있습니다. 일정 예약 사이트 (<a href='https://www.aws.training/certification'>https://www.aws.training/certification</a>)를 통해 접속을 합니다. 

<br>

![image](https://github.com/user-attachments/assets/645c3e2c-8245-466e-b7c9-79fa258c74ba)
&nbsp;해당 링크로 접속하게 되면 위와 같은 AWS Training and Certification 페이지가 나오게 됩니다. 여기서 로그인을 진행합니다.

<br>

![image](https://github.com/user-attachments/assets/9dfda67e-4469-46a2-937f-12d95a06620b)
&nbsp;로그인하는 방법은 위와 같이 여러가지가 있지만 저의 경우 일반적인 케이스이기 때문에 AWS Builder ID로 로그인을 진행합니다. Builder ID가 없다면 따로 생성해야 합니다.

<br>

![image](https://github.com/user-attachments/assets/8dec60d6-2d23-4617-860c-b8afb80db790)
&nbsp;Builder ID를 생성하고 로그인하셨다면 다음에는 AWS Certification 계정을 생성하라고 합니다. 이것도 마찬가지로 생성합니다.

<br>

![image](https://github.com/user-attachments/assets/a81553b1-e6bd-47de-b8d7-e327515e7d13)
&nbsp;다음으로는 필요한 정보에 대해 입력하는 절차입니다. Agreement (동의서), Name Confirmation (이름 확인), Mailing Address (이메일 확인), Profile questions (프로필 정보 / 개인 경력 정보)를 차례대로 입력합니다.

<br>

![image](https://github.com/user-attachments/assets/f55a8d8e-3c32-4cfc-9476-ab0f03be7d59)
&nbsp;전부 다 입력하고 나면 대시보드가 나오는데 왼쪽 메뉴에서 `[EXAM REGISTRATION] - [Schedule an exam]` 메뉴를 선택하고 `data`로 검색해서 자격증을 찾아 `AUTHORIZE`를 클릭하면 `SCHEDULE` 버튼으로 변경되는 것을 보실 수 있습니다. 

<br>

![image](https://github.com/user-attachments/assets/bc927919-d02c-425d-a677-ed9b2c005ef4)
&nbsp;이 SCHEDULE을 선택하면 위와 같은 화면이 뜨는데 오프라인(In person at a test center) 또는 온라인 (Online with OnVUE)를 선택합니다.

<br>

![image](https://github.com/user-attachments/assets/12400cd6-64cd-4164-b1db-6fd667e071ab)
&nbsp;저는 온라인으로 선택하고 Next를 선택했습니다. 그러면 이후에 원하는 언어와 정책 동의, 시험 문항 언어, 그리고 시간 등을 지정하고 예약을 하면 됩니다.

<br><br>

## 시험 준비

![image](https://github.com/user-attachments/assets/1ca26b1b-9902-4af3-ac42-9a1b85d9d62a)

&nbsp;일정 예약하는 방법에 대해 알아봤다면 다음으로는 시험 준비입니다. AWS에서 제공해주는 준비 과정으로 진행해도 되고 따로 공부해도 되는데 저는 이 과정을 따라해보려고 합니다. 

<br><br>

&nbsp;하나씩 더 공부를 하면서 자격증을 준비해보도록 하겠습니다.