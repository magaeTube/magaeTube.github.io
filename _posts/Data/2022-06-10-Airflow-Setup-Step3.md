---
title:  "Airflow 구축 및 변천사 - 개선"
date:   2022-06-10 09:00:00 +0900
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


![airflow](https://user-images.githubusercontent.com/78892113/172186672-025cb8a1-cddf-4b90-b08c-a8c3634cbb90.png){: .align-center}{: width="80%" height="80%"} 

<br>

&nbsp;안녕하세요 마개입니다.  
이전 글에서는 Airflow를 구축하고 기본적인 설정을 하는 과정을 정리하였습니다. 이번에는 기본적인 환경에서 겪은 불편함과 이를 해결하면서 개선하는 과정에 대해 정리하겠습니다. 
{: .notice--info}

<br><br><br>

# 알림 방식

<br>

&nbsp;알림 방식을 메일을 통해 받는 것으로 초기 구축을 했었습니다. 처음에는 DAG 자체가 몇 개 없었기 때문에 Task 별로 메일이 와도 문제가 없었지만 DAG가 늘어나면 날수록 5배 정도의 양으로 메일이 오기 때문에 점점 문제가 되었습니다. 과도하게 메일이 오는 문제가 발생하였습니다 (DAG가 늘어나고 사이즈가 커지는 것은 좋지만 메일 알림은 심했다....ㅎㅎ)

&nbsp;그렇기에 알림 방식을 변경하기로 했습니다. 메일보다는 회사에서 사용 중인 메신저 슬랙을 이용하기로 하였습니다.

​<br><br>

## 실행 방식

&nbsp;Airflow에서는 **<span style="color:red">SlackAPIPostOperator</span>**라는 슬랙 메시지 보내기용 Operator를 제공합니다. 이것을 통해서 원할 때 정해진 채널로 메시지를 보내는 방식입니다. Airflow에서 DAG의 default_args들 중에 **<span style="color:red">on_success_callback</span>**과 **<span style="color:red">on_failure_callback</span>**이 있습니다. 이는 각각 DAG가 <u>성공했을 때 또는 실패했을 때 실행할 콜백 함수</u>를 지정하는 것입니다. 이 속성들을 이용해서 성공했을 때 또는 실패했을 때 슬랙 메시지를 보내는 방식입니다.  
(최신 버전에서는 <u>SlackAPIPostOperator가 deprecated 되었다는 말</u>도 있어 각자의 버전에 대해 확인하시길 바랍니다.)

<br><br>

## 준비물

&nbsp;슬랙을 이용하여 알림을 보내기 위해서는 몇 가지 준비물과 세팅이 필요합니다. 

* **채널**
* **토큰**
* **connection**

&nbsp;첫 번째로 **<span style="color:red">슬랙 채널</span>**이 필요합니다. Airflow의 실행 여부에 따라 메시지를 보낼 채널을 정해야 합니다. 저의 경우 채널을 하나 생성한 후 Airflow와 관련된 팀원들을 모두 초대하여 같이 메시지를 받도록 하였습니다.

<br>

​![image](https://user-images.githubusercontent.com/78892113/173606943-a6a4d4e9-a9e5-4727-a22e-74df50920e27.png){: .align-center}{: width="60%" height="60%"} 

<br>

&nbsp;두 번째로는 **<span style="color:red">토큰</span>**입니다. 

슬랙 API를 이용해서 메시지를 보내기 위해서는 슬랙 토큰이 필요합니다. 이 과정에서는 토큰을 만들고 Webhook 설정하는 것에 대해 다루지 않겠습니다. 

​
&nbsp;세 번째로는 **<span style="color:red">connection</span>**입니다.

&nbsp;토큰을 생성하여 정보를 가지고 있다면 이를 이용하여 메시지를 보낼 것입니다. 코드에 토큰 정보를 그대로 이용하면 보안에 문제가 생기기 때문에 Airflow의 Connections를 이용합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/173607103-053a4819-1886-42b0-a73f-b4184e7bc3b8.png){: .align-center}{: width="80%" height="80%"} 

<br>

&nbsp;Airflow Webserver 상단에 보면 <u>[Admin] - [Connections]</u> 메뉴가 있습니다. 이 메뉴에서 좌측 상단에 "+" 버튼을 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/173607189-cdd46132-ca57-4d05-b8f3-6ed3d8bb1dd9.png){: .align-center}{: width="80%" height="80%"} 

<br>

&nbsp;그러면 위와 같이 **Conn Id, Conn Type, Password** 부분을 작성합니다. 

&nbsp;Conn Id는 Connections에서 구분할 ID로 여기서 지정한 이름을 다른 곳에서도 이용합니다. Conn Type의 경우 마땅한 것이 없어 SSH로 지정하였고 슬랙 토큰 정보는 Password 부분에 입력하고 Save 버튼을 클릭합니다. 

<br><br>

## 개발

&nbsp;이제 기본 준비는 되었고 파이썬에 개발을 하면 됩니다. 위에서 설명했던 **<span style="color:red">SlackAPIPostOperator</span>**를 이용하였습니다. 

```python
from airflow.hooks.base import BaseHook
from airflow.providers.slack.operators.slack import SlackAPIPostOperator
from dateutil.relativedelta import relativedelta
from datetime import datetime


class SlackAlert:
    def __init__(self, channel):
        self.slack_channel = channel
        self.slack_token = BaseHook.get_connection("slack").password

    def slack_success_alert(self, context):
        now = datetime.now().strftime("%Y-%m-%d %H:%M:%S")

        dag = context.get("task_instance").dag_id
        task = context.get("task_instance").task_id
        exec_date = (context.get("execution_date") + relativedelta(hours=9)).strftime("%Y-%m-%d %H:%M:%S")
        log_date = now
        log_url = context.get("task_instance").log_url
        try_number = context.get("task_instance").try_number - 1
        max_tries = context.get("task_instance").max_tries
        max_tries = 1 if max_tries == 0 else max_tries

        message = (
            f'`DAG` : {dag}'
            f'\n`Task` : {task} (try {try_number} of {max_tries})'
            f'\n`Execution Time (KST)` : {exec_date}'
            f'\n`Log Time (KST)` : {log_date}'
        )

        attachments = {
            "pretext": ":large_green_circle: Task Succeeded.",
            "text": message,
            "color": "good",
            "actions": [
                {
                    "name": "view log",
                    "text": "View log",
                    "type": "button",
                    "url": log_url,
                    "style": "primary"
                }
            ]
        }

        alert = SlackAPIPostOperator(
            task_id="slack_succeeded",
            channel=self.slack_channel,
            token=self.slack_token,
            text="",
            attachments=[attachments]
        )

        return alert.execute(context=context)
```

여기서 살펴봐야 할 부분은 다음과 같습니다.

<br><br>

### 토큰 가져오기

&nbsp;준비물을 통해 토큰을 connection에 등록했는데 SlackAPIPostOperator에서 이용하기 위해 connection에서 정보를 가져와야 합니다. 이를 이용하기 위해 **<span style="color:red">BaseHook</span>**으로 정보를 가져옵니다. 

```python
self.slack_token = BaseHook.get_connection("slack").password
```

<br><br>

### SlackAPIPostOperator

```python
        alert = SlackAPIPostOperator(
            task_id="slack_succeeded",
            channel=self.slack_channel,
            token=self.slack_token,
            text="",
            attachments=[attachments]
        )
```

&nbsp;일반적으로 Operator를 사용해서 메시지를 보낼 때 **<span style="color:red">text</span>**부분에 전송될 메시지를 작성합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/173607714-d2c3e5b8-2e6e-4a27-8de8-63f1b388e476.png){: .align-center}{: width="80%" height="80%"} 

![image](https://user-images.githubusercontent.com/78892113/173607739-58749c04-8581-4da5-b5be-64f9165f2b29.png){: .align-center}{: width="80%" height="80%"} 

<br>

&nbsp;처음에 슬랙으로 메시지를 보낼 때는 **<span style="color:red">text</span>**만 이용하여 위와 같이 메시지를 보냈었습니다. 맨 앞에 초록색 원과 빨간색 원을 이용해서 <u>성공/실패 여부를 시각화</u>하였고 나머지는 DAG, Task, 실행 시간 등등을 이용하였습니다. 

&nbsp;위와 같은 형태로 알림을 보내면서 이용을 했었는데 <u>모바일을 통해 성공/실패 여부가 한눈에 잘 들어오지 않고 로그 링크가 나오기 때문에 지저분</u>해 보였습니다. 이에 따라 성공/실패 여부가 더 잘 보이게 하고 싶었고 버튼 하나로 로그를 확인할 수 있도록 하고 싶어서 수정을 진행했습니다.

```python
        attachments = {
            "pretext": ":large_green_circle: Task Succeeded.",
            "text": message,
            "color": "good",
            "actions": [
                {
                    "name": "view log",
                    "text": "View log",
                    "type": "button",
                    "url": log_url,
                    "style": "primary"
                }
            ]
        }
```

&nbsp;text보다는 **<span style="color:red">attachments</span>** 속성을 이용하여 형태를 변경하였습니다. **<span style="color:red">color</span>** 속성에 들어가는 값에 따라 앞에 색상이 달라지게 됩니다.(<u>여기서 good이면 초록색, danger면 빨간색으로 자동 적용됩니다.</u>) **<span style="color:red">actions</span>**에는 버튼을 추가하여 클릭 시에 해당 Task의 로그 화면으로 넘어가게 만들었습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/173607900-43fce341-0b04-4f24-b480-11069b327b53.png){: .align-center}{: width="60%" height="60%"} 

![image](https://user-images.githubusercontent.com/78892113/173607924-c91f4076-4de4-485b-bf83-b6d55f6bfadf.png){: .align-center}{: width="60%" height="60%"} 

<br>

&nbsp;현재의 최종적인 모습은 위와 같습니다. 이렇게 하니 성공/실패 여부가 더욱 눈에 띄고 <u>View log버튼으로 로그는 보는 것</u>이 편해졌습니다. 

&nbsp;여기서 추가하고 싶은 것은 실패했을 때 <u>Retry를 할 수 있는 버튼을 추가</u>하고 싶은 것입니다. 실패하면 Airflow에 다시 들어가서 Retry 버튼을 눌러야 하는데 그보다는 슬랙에서 직접 Retry가 되도록 추가하고자 합니다. (추후에..)

&nbsp;이렇게 슬랙용 Class를 따로 만들고 DAG에서 불러오는 것은 간단합니다. 

```python
from module.SlackUtil import SlackAlert

alert = SlackAlert("#채널명")

default_args = {
    ...
    "on_success_callback": alert.slack_success_alert,
    "on_failure_callback": alert.slack_fail_alert
}
```

&nbsp;default_args 부분에서 기존에 있던 email 부분은 삭제하고 **<span style="color:red">on_success_callback</span>**과 **<span style="color:red">on_failure_callback</span>** 부분을 추가합니다. 

&nbsp;이렇게 하니 이메일을 받을 때보다는 보기도 편해지게 되었습니다.

<br><br><br>

# 로그 저장소 변경

<br>

&nbsp;DAG가 점점 늘어나면서 로그의 크기도 점점 쌓이게 됩니다. 처음에 Airflow를 구축할 때 아무 설정도 하지 않으면 `$AIRFLOW_HOME/airflow/logs`에 로그가 쌓이게 됩니다. 점점 쌓이다 보니 저장소를 변경해야겠다는 생각이 들어 **<span style="color:red">S3</span>**로 변경을 하였습니다. 

<br><br>

## 설정

&nbsp;S3에 로그 데이터를 쌓기 위해서는 <u>버킷을 미리 생성</u>해야 합니다. 

```
[logging]
remote_logging = True
remote_base_log_folder = s3://버킷명
```

&nbsp;버킷을 생성하였다면 airflow.cfg에서 위와 같이 해당 부분에 설정을 하면 log 데이터들은 S3에 쌓이게 됩니다. 아무리 S3에 쌓는다고 하지만 기간이 지나면 엄청난 양을 보유하기 때문에 <u>최근 한 달 정도의 로그만 가지고 나머지는 지우는 작업을 매일 하게 만들었습니다.</u>

<br><br><br>

# Executor 변경

<br>

&nbsp;기존에 5, 6개의 DAG에서 시작해서 30~40개쯤으로 늘어나다 보니 메모리적인 부분이나 실행 부분에서 Worker를 늘리는 것이 좋겠다고 생각했습니다. 그래서 기존에 사용하던 LocalExecutor를 **<span style="color:red">CeleryExecutor</span>**로 변경하는 것이 좋겠다고 판단하여 진행하였습니다.

&nbsp;CeleryExecutor를 이용하기 위해서는 Celery를 컨트롤할 브로커가 필요합니다. Airflow에서는 RabbitMQ나 **<span style="color:red">Redis</span>**를 이용하는데 저는 Redis를 이용하였습니다. Redis는 Docker를 이용해서 설치하였습니다. 

<br><br>

## 설정 변경

&nbsp;CeleryExecutor를 이용하기 위해서 **<span style="color:red">airflow.cfg</span>**에서 설정들을 몇 개 변경해야 합니다.

```
[core]
executor = CeleryExecutor

[celery]
broker_url = redis://REDIS호스트주소:포트(기본6379)/0
result_backend = db+mysql://아이디:비밀번호@MYSQL호스트주소:포트/디비명
```

&nbsp;위와 같이 airflow.cfg 설정 파일들을 변경하면 됩니다.

<br><br>

## 실행

설정하였으니 실행을 해봅니다.

```sh
$ airflow webserver -p 8080 -D
$ airflow scheduler -D
$ airflow celery worker -D
$ airflow celery flower -D
```

&nbsp;하나씩 실행을 시켜보면 $AIRFLOW_HOME 경로에 각 프로세스마다 .pid가 생성됩니다. 이 파일에는 각자의 프로세스 ID가 적혀있는 것을 확인할 수 있습니다.

<br><br>

## Troubleshooting

* **umask**

&nbsp;DAG을 통해 Elasticsearch에 반영할 index 파일들을 생성하는 프로세스가 있습니다. 

```sh
-rw-rw-r-- 1 tomcat tomcat 3309948 11월 17 11:05 input-5000.json
```

&nbsp;CeleryExecutor로 변경하기 전에는 위와 같은 형태로 파일 권한이 664였습니다. 

```sh
-rw------- 1 tomcat 3458722 11월 18 11:05 input-5000.json
```

&nbsp;그러나 CeleryExecutor로 변경한 후에는 위와 같이 권한이 600으로 변경되었습니다. 이로 인해 Jenkins에서 파일을 제대로 읽지 못해서 Elasticsearch에 반영이 되지 않았습니다. 이는 Airflow에서 **<span style="color:red">umask 설정</span>**에 따른 문제입니다.

```
[celery]
# worker_umask = 0o077
worker_umask = 0002
```

&nbsp;airflow.cfg 파일을 보면 기본적으로 worker_umask는 `0o077`로 되어 있습니다. 권한을 변경하여 `0002`로 변경하면 정상적으로 파일 권한이 664로 생성됩니다.

<br><br><br>

# Docker 변경

<br>

&nbsp;CeleryExecutor로 변경되면서 LocalExecutor보다 <u>관리해야 할 포인트</u>도 늘어나고 기존에 한대의 서버(PC)에서 다 운영하다 보니 <u>메모리도 부족</u>하여 다운되는 현상이 종종 일어났습니다. 그때마다 재부팅을 하고 명령어를 다시 날려서 실행시키고 이런 작업을 반복하다 보니 관리가 더 편하면 좋겠다는 생각이 들었습니다. 

&nbsp;이에 따라 서로에게 영향이 가지 않도록 **<span style="color:red">Worker와 나머지를 분리</span>**하고 모든 것들을 **<span style="color:red">Docker로 변경</span>**해야겠다는 생각을 했습니다. 

&nbsp;Airflow 구축 초기에도 했던 고민이었지만 Docker를 사용하는 분들이 없었기 때문에 직접 구축했었는데 이러한 불편함을 겪는 것보다는 <u>사용 가이드를 만들어 Airflow를 이용하는 팀원들에게 알려주는 것이 낫겠다는 생각</u>을 했습니다. 이에 따라 Docker로 변경을 하기 시작했습니다.

&nbsp;Docker로 변경할 때 고민이 되었던 부분은 어떤 이미지를 사용할지 고민이었습니다. 많은 분들이 puckel/docker-airflow 이미지를 이용했는데 저는 이참에 Airflow 버전도 2.x대로 옮기고 싶었고 파이썬 패키지 부분이나 OS 부분 등을 커스텀 마이징 해야 할 부분들이 있어서 Apache에서 제공하는 공식 Airflow 이미지를 사용하기로 했습니다. 기존에 있던 환경과 비슷하게 하기 위해 **<span style="color:red">apache/airflow:2.1.0-python3.8</span>** 이미지를 이용했습니다. 

```dockerfile
FROM apache/airflow:2.1.0-python3.8
MAINTAINER "shkim1@tidesquare.com"
...
USER root
RUN apt-get update
...
RUN if [ -e "/requirements.txt" ]; then pip install --no-cache-dir -r /requirements.txt; fi
```

&nbsp;Dockerfile에서는 위와 같이 구성하고 docker-compose에서는 해당 Dockerfile을 빌드 하는 방식으로 구성했습니다. (자세한 내용은 생략되었습니다.)

&nbsp;이때 Airflow에 들어가는 설정 내용은 각 서비스에 등록하기보단 **<span style="color:red">.env</span>** 파일 하나로 관리를 했습니다.

```
AIRFLOW__CORE__EXECUTOR: CeleryExecutor
AIRFLOW__CORE__DEFAULT_TIMEZONE: 'Asia/Seoul'
...
```

Airflow에서 사용할 설정 내용은 위와 같이 **<span style="color:red">.env</span>**파일에 모두 작성했는데 Docker에서 사용할 환경 변수의 규칙은 따로 있습니다.

```
# airflow.cfg
[core]
default_timezone = Asia/Seoul

# Docker
AIRFLOW__CORE__DEFAULT_TIMEZONE: 'Asia/Seoul'
```

&nbsp;위의 줄은 airflow.cfg에서 사용되는 작성법이고 아래는 Docker 용 작성법입니다. **<span style="color:red">AIRFLOW__"[] 안에 그룹명"__"속성명"</span>** 형태로 작성되는 것을 볼 수 있습니다.

&nbsp;DAG에서 이용했던 config나 resource 파일들의 경로는 모두 Docker에 맞게 변경을 했습니다.

<br><br>

## Troubleshooting

* **UID, GID**

&nbsp;Airflow를 이용해서 DAG가 실행될 때 스크립트를 실행하거나 파일을 Read, Write 하는 작업이 있다 보니 Docker에서 `볼륨`을 지정하여 이용했습니다. 파일의 퍼미션 문제가 발생할 수 있는데 root 유저로 하는 방법도 있지만 이는 보안상 문제가 발생할 수 있습니다. 이 방법 말고 **<span style="color:red">UID</span>**와 **<span style="color:red">GID</span>**를 이용하여 해결했습니다. 

&nbsp;호스트에서 UID와 GID를 확인하는 방법은 리눅스에서 id 명령어를 실행하면 UID, GID 등 정보들이 나오게 된다. 이를 확인하고 <u>docker-compose에서 user 값을 변경</u>해서 이용했습니다. 

* Timezone

&nbsp;Docker로 변경을 하다 보니 컨테이너의 **<span style="color:red">Timezone</span>**을 다시 지정해 줘야 합니다. 저는 Dockerfile에서 지정을 해서 변경하였습니다.

```dockerfile
RUN ln -snf /usr/share/zoneinfo/Asia/Seoul /etc/localtime && echo Asia/Seoul > /etc/timezone
```

<br><br><br>

# 결론

<br>

&nbsp;처음 구축해놓은 Airflow에서의 알림 방식으로 인해 너무 자주 메일이 오는 불편함과 처리해야 하는 DAG 수도 증가해서 발생하는 메모리 부족 현상 등을 이유로 위와 같은 방식으로 개선 작업을 진행했습니다. 이전에는 서버가 자주 죽어서 평일 밤, 주말을 안 가리고 대응했었는데 이번 개선 작업을 통해 많은 부분이 해소된 것이 도움이 되었습니다. 