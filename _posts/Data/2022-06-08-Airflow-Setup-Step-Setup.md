---
title:  "Airflow 구축 및 변천사 - 구축"
date:   2022-06-08 09:00:00 +0900
categories:
  - Data
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
이전 글에서는 Airflow를 도입하게 된 배경과 선정 이유에 대해 정리하였습니다. 이번에는 구축하는 과정에 대해 내용을 정리해 보겠습니다. 
{: .notice--info}

<br>

관련 글들은 아래 링크로 확인하실 수 있습니다.

* <a href="https://magaetube.github.io/data/Airflow-Setup-Step-Intro/">Airflow 구축 및 변천사 - 도입 배경 및 선정</a>
* <a href="https://magaetube.github.io/data/Airflow-Setup-Step-Setup/">Airflow 구축 및 변천사 - 구축</a>
* <a href="https://magaetube.github.io/data/Airflow-Setup-Step-Improvement/">Airflow 구축 및 변천사 - 개선</a>
* <a href="https://magaetube.github.io/data/Airflow-Setup-Step-Monitoring/">Airflow 구축 및 변천사 - 모니터링</a>

<br>

![airflow](https://user-images.githubusercontent.com/78892113/172186672-025cb8a1-cddf-4b90-b08c-a8c3634cbb90.png){: .align-center}{: width="80%" height="80%"} 

<br><br><br>

# Airflow Architecture

<br>

&nbsp;구축하기 전에 앞서 간단히 Airflow Architecture에 대해 살펴보겠습니다.

![image](https://user-images.githubusercontent.com/78892113/173232579-0f165bad-a6ae-4d0f-8a49-49044f6ecd39.png)

&nbsp;가장 기본적인 Architecture는 위와 같습니다.
* **Webserver :** UI를 통해서 DAG과 태스크를 실행하고 관리하고 로그를 보는 등의 모니터링이 가능함  
* **Scheduler :** 스케줄링된 Workflow를 Trigger 하고 실행하기 위해 Executor로 Task를 보냄  
* **Executor :** 실행 중인 Task를 다루는 역할. 여러 종류가 있는데 각 Executor마다 실행 방식이 다름.
  * Sequential Executor : Metadata Database를 SQLite로 이용하여 한 번에 1개의 Task만 실행이 가능함
  * Local Executor : Metadata Database로 MySQL 또는 PostgreSQL을 이용하여 Task를 병렬 실행이 가능함 
  * Celery Executor : Worker를 Scale out 할 수 있는 구조(Celery)로 실행이 가능함. Celery를 이용하기 위해 RabbitMQ나 Redis가 필요함. 
  * Kubernetes Executor : Kubernetes를 이용하여 Pod를 생성하며 실행이 가능함
  * 그 외에 Debug Executor, CeleryKubernetes Executor, Dask Executor가 있음
* **Worker :** Task가 실제로 실행되는 것
* **Metadata Database :** Metadata를 저장하는 데이터베이스

<br><br><br>

# 구축하기 전 고민

<br>

&nbsp;설치를 하기 전에 앞서 고민을 하고 의견을 나누었던 사항들이 있습니다.

* 어떠한 **<span style="color:red">버전</span>**을 설치할 것인지 
* **<span style="color:red">직접</span>** 설치할 것인지, **<span style="color:red">Docker</span>**를 사용하여 설치할 것인지 
* 어떠한 **<span style="color:red">Executor</span>**를 설정할 것인지 

<br><br>

## 어떠한 버전을 설치할 것인가

&nbsp;Airflow는 성장이 매우 가파르고 빠르게 변화하고 있습니다. 현재 글을 작성하는 기준으로는 2.3.2 버전이 최신 버전으로 나와있습니다. 하지만 회사에 처음 구축을 할 때에는 <u>가장 안정화된 최신 버전이 1.10.14 버전</u>입니다. 아무것도 없는 상황이라서 최신 버전인 1.10.14 버전을 선택하였습니다.

<br><br>

## 직접 설치할 것인지 아니면 Docker를 사용하여 설치할 것인가

&nbsp;이 부분에서는 의견이 갈렸었습니다. **<span style="color:red">Docker</span>**를 이용하면 그나마 <u>쉽게 구축</u>할 수 있었는데 입사했을 때 듣기로는 회사에서 Docker를 사용하는 분이 없다고 들었습니다. 저는 Docker로 해서 편하게 관리하는 것이 좋겠다고 했었지만 Docker로 하면 유지 보수가 힘들고 빠르게 변화하기가 힘들다는 의견에 몇 번 의견을 나누다가 결국 **<span style="color:red">직접 구축</span>**하기로 결정하였습니다.

<br><br>

## 어떠한 Executor를 설정할 것인가

&nbsp;Executor의 경우 다른 회사들은 <u>Celery Executor나 Kubernetes Executor</u>를 많이 이용하는 것으로 알고 있었습니다. 회사에서 Docker를 사용하고 있지 않아 Kubernetes를 이용하는 것은 오버 스펙이라고 생각해서 이용하지 않았습니다. Celery를 고민했었는데 구축 당시에 실행되는 프로세스가 적었기 때문에 Local Executor로도 충분하겠다고 생각하여 **<span style="color:red">Local Executor</span>**를 선택하였습니다.

<br><br><br>

# 구축하기

<br>

&nbsp;위와 같은 고민을 거친 후 구축을 하기 시작했습니다. 구축 당시 스펙은 아래와 같았습니다.

* ​**OS** : Ubuntu 20.04 (Focal Fossa)
* **Python** : 3.8 
* **Airflow** : 1.10.14
* **Metadata Database** : MySQL 8 (AWS RDS)

&nbsp;OS나 Python의 경우 서버에 기본적으로 세팅되어 있는 버전으로 진행을 하였습니다. Metadata Database의 경우 PostgreSQL을 쓰는 경우가 많지만 회사에서 MySQL을 사용하기 때문에 MySQL로 진행하였습니다. AWS의 RDS를 이용하여 미리 세팅하고 데이터베이스도 생성했습니다.

&nbsp;파이썬의 경우 Ubuntu 20.04에는 3.8 버전이 기본적으로 설치되어 있습니다. 이 상황에서 저는 venv를 설치하여 따로 패키지를 관리할 수 있도록 가상환경을 만들어서 이용했습니다.

<br><br>

## 기본 설정하기

&nbsp;가상환경을 실행하고 당시의 Airflow 최신 버전인 1.10.14를 설치하였습니다.

&nbsp;Airflow를 설치하고 초기화를 하면 **<span style="color:red">airflow.cfg</span>** 파일이 생성되는데 해당 파일 안에  Metadata Database와 Executor 등 몇 가지 설정을 해줍니다.

```
[core]
executor = LocalExecutor
sql_alchemy_conn = mysql://아이디:비밀번호@host주소:3306/DB명
load_examples = False
```

<br><br>

## Timezone 변경하기 

&nbsp;Airflow Web UI를 확인해 보면 Timezone이 맞지 않은 것을 확인할 수 있습니다. DAG를 생성하면 내가 예상한 시간에 실행되지 않고 엉뚱한 시간에 실행이 됩니다. Airflow에서는 사용자의 위치에 따라 <u>Timezone을 자동으로 변경해 주지 않습니다.</u> 그렇기 때문에 기본으로 설정되어 있는 Timezone에 따라 DAG가 실행되고 이것은 정확한 실행 시간에 대해 파악이 힘들기 때문에 Timezone을 변경해야 합니다. 더 낮은 Airflow 버전에서는 한국 timezone에 대한 지원이 힘들다는 의견들이 있었는데 다행히 1.10.14 버전에서는 지원이 가능하여 변경 작업을 하였습니다. <u>기본 Timezone 설정은 UTC</u>로 되어있는데 Airflow 설정과 DAG 설정 두 가지 부분에서 변경을 해야 합니다. 

<br><br>

### Default Timezone 변경

&nbsp;첫 번째로 Airflow를 실행할 때 참고하는 설정 파일 **<span style="color:red">airflow.cfg</span>**를 변경해야 합니다.

```
[core]
default_timezone = Asia/Seoul

[webserver]
default_ui_timezone = Asia/Seoul
```

&nbsp;기본적으로 각각의 값은 **<span style="color:red">utc</span>**로 되어있는데 이를 변경합니다. 변경할 때에는 kst가 아니라 <u>IANA timezone 데이터</u>인 **<span style="color:red">Asia/Seoul</span>**로 변경을 해야 합니다.

​&nbsp;Asia/Seoul이 아니라 다른 곳에 계시다면 아래 <a href="https://secure.jadeworld.com/JADETech/JADE2020/OnlineDocumentation/content/resources/encyclosys2/jadetimezone_class/ianawindowstimezonemapping.htm">링크</a>를 통해 맞는 정보를 찾아서 적용하시면 됩니다.

<br><br>

### DAG에서의 Timezone 변경

&nbsp;Airflow 설정을 통해 Timezone을 변경하였다면 DAG를 작성할 때에도 Timezone을 변경해야 합니다. DAG를 작성할 때 <u>start_date를 참조</u>하는데 이때 설정한 Timezone에 따라 영향이 가기 때문입니다. 잘못하면 내가 원하지 않은 시간에 실행이 될 수 있습니다. 변경하는 방법에는 파이썬 패키지 중 하나인 **<span style="color:red">pendulum</span>**을 이용합니다. pendulum은 Airflow를 설치하면 자동으로 같이 설치됩니다.

```python
from datetime import datetime
import pendulum

local_tz = pendulum.timezone("Asia/Seoul")

default_args = {
    ...,
    "start_date": datetime(2022,6,7, tzinfo=local_tz),
    ...
}
...
```

<br><br><br>

# 알림 받기

<br>

&nbsp;기본적으로 Airflow를 구축하고 기존에 크론잡을 통해 실행되던 프로세스를 <u>DAG로 작성</u>하여 옮겼습니다. 

​&nbsp;DAG들이 실행되면서 업무시간에는 Webserver를 띄워놓은 채로 새로고침하며 모니터링했습니다. (초반에는 무식하게 꾸준히...) 이렇게 하염없이 바라보기에는 다른 업무들도 해야 했기에 알림을 받는 게 필요하다고 생각했습니다. 

​&nbsp;기존에 레포트 결과 송신용 **<span style="color:red">이메일 계정</span>**이 따로 있었기에 이를 이용하기로 했습니다. 

<br><br>

## 설정

&nbsp;이메일의 경우 **<span style="color:red">airflow.cfg</span>**에서 설정을 해야 합니다. 

```
...(생략)...
[smtp]
smtp_host = 이메일호스트주소
smtp_port = 이메일포트
smtp_user = 이메일계정주소
smtp_password = 이메일패스워드
smtp_mail_from = 발신자명
...(생략)...
```

&nbsp;설정은 간단하게 이메일 관련된 정보들을 위와 같이 작성하면 됩니다. 그러면 이메일을 이용할 때 해당 정보들을 이용하여 발신이 됩니다.

<br><br>

## 개발

&nbsp;DAG를 생성할 때 이메일의 경우 **<span style="color:red">default_args</span>**에 내용을 추가하면 됩니다.

```python
from datetime import datetime
import pendulum

local_tz = pendulum.timezone("Asia/Seoul")

default_args = {
    ...,
    "start_date": datetime(2022,6,7, tzinfo=local_tz),
    "email": ["수신자 이메일 주소1", "수신자 이메일 주소2"],
    "email_on_success": True,     # 성공했을 때 이메일 발신 여부
    "email_on_failure": True,    # 실패했을 때 이메일 발신 여부
    "email_on_retry": True,      # 재시도했을 때 이메일 발신 여부
    ...
}
...
```

&nbsp;위와 같이 설정을 하면 DAG가 성공/실패하거나 재시도를 할 때 지정한 수신자들에게 이메일을 발신하게 됩니다.

<br><br><br>

# 그 외

<br>

&nbsp;어느 정도 기본 세팅이 완료되고 새로운 DAG를 추가하고 운영하면서 필요했던 것들 중 중요한 것만 보겠습니다.

<br><br>

## catchup

&nbsp;크론잡에서 실행 중이던 프로세스를 신규 DAG로 옮겨왔습니다. 이전부터 실행되던 프로세스였기 때문에 <u>start_date를 과거로 설정</u>하였습니다. 그러고 Airflow에 등록하였더니 해당 DAG에 큐가 엄청 쌓이더니 실행이 되었습니다. 내용을 확인해 보니 <u>start_date부터 현재까지 지정한 interval로 큐가 쌓인 것</u>이었습니다. 필요에 따라 이런 경우가 필요할 수 있지만 이렇게 과거에 실행되지 않았던 부분을 자동으로 실행되지 않기 위해서는 **<span style="color:red">catchup</span>**이라는 설정을 변경해야 합니다. 

```python
from airflow import DAG

default_args = {
    ...
}

dag = DAG(
    dag_id="test",
    default_args=default_args,
    schedule_interval="0 2 * * *",
    catchup=False
)
```

&nbsp;위와 같이 **<span style="color:red">catchup</span>**의 경우 False로 설정하면 과거 실행 부분은 진행하지 않습니다.

<br><br>

## depends_on_past

&nbsp;1시간마다 실행되는 프로세스가 있습니다. 해당 프로세스의 여러 Task 중에 하나의 Task가 에러가 나서 내용을 수정하고 다음 시간을 기다렸습니다. 다음 interval에서 프로세스가 실행이 되었는데 <u>문제가 되었던 Task에서 실행이 되지 않고 no_status 상태로 있는 것을 확인</u>할 수 있었습니다. 해당 내용에 대해 확인을 해보니 depends_on_past에 대한 설정이 필요했습니다. **<span style="color:red">depends_on_past</span>**는 위와 같이 과거에 같은 Task의 성공/실패 여부에 따라 현재의 같은 Task를 실행할지 말지에 대한 설정인데 저의 경우 과거에 실패했더라도 현재 다시 실행되야 하기 때문에 무시하도록 설정이 필요했습니다.

```python
from datetime import datetime
import pendulum

local_tz = pendulum.timezone("Asia/Seoul")

default_args = {
    ...,
    "start_date": datetime(2022,6,7, tzinfo=local_tz),
    "email": ["수신자 이메일 주소1", "수신자 이메일 주소2"],
    "email_on_success": True,    
    "email_on_failure": True,
    "email_on_retry": True,
    "depends_on_past": False
    ...
}
...
```

&nbsp;**<span style="color:red">depends_on_past</span>**의 경우 default_args을 설정할 때 지정합니다. 해당 속성을 지정하여 위와 같은 문제를 해결했습니다.

​&nbsp;위 게시글과 같이 Airflow를 Local Executor로 직접 구축하고 기존의 크론잡에 있던 프로세스들을 DAG로 구성하였습니다. 이후 알림의 경우나 운영을 하면서 발생했던 사소한 문제들에 대한 내용을 정리해보았습니다.