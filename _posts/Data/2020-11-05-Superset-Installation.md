---
title:  "[BI] Apache Superset 설치"
date:   2020-11-05 00:00:00 +0900
categories:
  - Data
  - Superset
tags:
  - Data
  - Superset
toc: true
toc_sticky: true
toc_label: "Superset 설치"
header-img: https://user-images.githubusercontent.com/78892113/209071809-f02c0a94-a658-4671-a563-116f4872a9e1.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
BI 툴 중 오픈소스인 Apache Superset에 대한 설치 작업을 진행해보겠습니다. 공식 홈페이지의 문서에 따라 설치를 진행하였습니다.  
설치 OS 환경은 Ubuntu 20.04에서 진행했습니다. 
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209071809-f02c0a94-a658-4671-a563-116f4872a9e1.png){: .align-center}{: width="70%" height="70%"} 

<br>

* **참고 URL** : <a href="https://superset.apache.org/docs/installation/installing-superset-from-scratch/">링크</a>

<br><br>

# OS 의존성 설치

`Superset`은 메타데이터에 데이터베이스 연결 정보를 저장하는 작업을 합니다. 이때 파이썬 라이브러리를 이용해서 암호화하기 때문에 관련된 의존성들을 설치하는 작업을 진행해야 합니다. 저는 Ubuntu 20.04 버전에 맞는 의존성들을 설치하였습니다. 

```sh
sudo apt-get install build-essential libssl-dev libffi-dev python3-dev python3-pip libsasl2-dev libldap2-dev
```

<br><br>

# 파이썬 가상 환경 세팅

`Superset`에서는 파이썬에서 **가상환경**을 만들어서 그 안에서 Superset을 설치할 것을 권장하고 있습니다. 그래서 python에서 제공하는 **virtualenv**를 설치하고 이용합니다. 

```sh
# 가상환경 설치
pip install virtualenv

# 확인 및 적용
python3 -m venv venv
source venv/bin/activate
```

![virtualenv 설치](https://user-images.githubusercontent.com/78892113/209083774-3d67c43e-4444-4753-b5c8-8615a40d17ee.png)

<br>

![image](https://user-images.githubusercontent.com/78892113/209083787-ed9eff93-8015-434b-b2af-6de0514c3950.png)

`python3 -m venv venv` 작업을 진행할 때 위와 같이 설치가 되지 않을 수도 있습니다. 그럴 때는 중간에 나와있는 명령어처럼 작업을 한 후에 다시 진행합니다.

<br>

```sh
sudo apt-get install python3-venv
```

![image](https://user-images.githubusercontent.com/78892113/209083800-23a7d6ef-698c-48fd-9c2a-c7960d03a171.png)

에러가 없이 진행되는 것을 확인하실 수 있습니다.

<br><br>

# Superset 설치 및 설정

`pip`를 이용해 **Superset**을 설치하도록 합니다.

```sh
pip3 install apache-superset
```

![image](https://user-images.githubusercontent.com/78892113/209083825-bc664607-6262-41b1-bb3b-7064830441ee.png)

설치가 완료되면 몇 가지 세팅을 해야 합니다.  
첫 번째로 Superset에서 메타데이터로 사용될 데이터베이스를 초기화해야 합니다.

<br>

```sh
superset db upgrade
```

![image](https://user-images.githubusercontent.com/78892113/209083845-fc93bc49-c6f6-4db5-8e4f-d8d45c3f19b3.png)

위에 가상환경 작업을 거치지 않고 그냥 파이썬에서 진행한다면 에러가 발생합니다. 꼭 가상환경 작업을 거친 후 가상환경 상에서 진행해주세요.

<br>

데이터베이스 초기화가 완료되면 다음으로 나머지 세팅을 진행합니다.

```sh
# admin 계정 만들기
export FLASK_APP=superset
superset fab create-admin

# 예제 데이터 불러오기
superset load_examples

# default role과 permission 생성
superset init

# 8088 포트 웹서버로 실행하기
superset run -p 8080 --with-threads --reload --debugger
```

![admin 계정 설정](https://user-images.githubusercontent.com/78892113/209083866-e701f3a7-180b-4ed1-8b29-f222854db3b6.png)

![예제 데이터 불러오기](https://user-images.githubusercontent.com/78892113/209083887-ad2ee211-ac0c-403b-aaa8-2a1dfc18325a.png)

![default role과 permission 생성](https://user-images.githubusercontent.com/78892113/209083901-8b40f508-ab49-43b7-b6ee-47e50d6514e8.png)

![실행하기](https://user-images.githubusercontent.com/78892113/209083914-f5442b2f-17d9-42ee-869a-f3d02017c32b.png)

<br><br>

# 결과 확인

<a href="http://localhost:8088">http://localhost:8088</a>로 접속하여 잘 뜨는지 확인합니다.

![로그인 페이지](https://user-images.githubusercontent.com/78892113/209083941-2518094e-af49-46f2-bb51-dffd0ab07b59.png)

위와 같이 로그인 화면이 제대로 뜬다면 앞에서 설정한 계정과 비밀번호를 입력하여 로그인합니다.

<br>

![메인 페이지](https://user-images.githubusercontent.com/78892113/209083957-b6f09069-d704-420d-9318-b0861bf475bc.png)

로그인해서 위와 같이 나오면 설치는 완료되었습니다.