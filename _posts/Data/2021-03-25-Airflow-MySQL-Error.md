---
title:  "[Airflow] Airflow 실행시 MySQLdb 에러"
date:   2021-03-25 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - DataEngineering
  - Airflow
toc: true
toc_sticky: true
header-img: https://user-images.githubusercontent.com/78892113/169574664-4efcf70e-f846-4919-9a00-730e8f799fe6.png

header-mask: true

---

![image](https://user-images.githubusercontent.com/78892113/169574664-4efcf70e-f846-4919-9a00-730e8f799fe6.png){: .align-center}{: width="80%" height="80%"}

<br>

&nbsp;안녕하세요 마개입니다. Airflow의 Executor를 SequentialExecutor에서 LocalExecutor로 변경을 하기 위해 Metadata 저장소로 MySQL을 선정하였습니다. 보통은 Airflow에서 PostgreSQL을 많이 사용하지만 사내에서는 이미 MySQL을 사용하고 있었기 때문에 MySQL을 지정하였습니다.
{: .notice--info}

<br>

Executor를 바꾸고 Metadata 저장소를 변경하기 위해 `airflow.cfg` 내용을 변경해야 합니다.

```config
[core]
executor = LocalExecutor
sql_alchemy_conn = mysql://USER명:PASSWORD@MySQL호스트:포트/DB명
```

위와 같이 변경을 하고 Airflow 실행 명령어를 날리니 아래와 같은 에러가 발생하였습니다.

<br>

```python
ModuleNotFoundError: No module named 'MySQLdb'
```

![image](https://user-images.githubusercontent.com/78892113/169578381-14452f8b-9f0b-4d74-b948-b6784f50d6e4.png){: .align-center}{: width="100%" height="100%"}

<br><br>

Airflow에서 SQL Engine을 이용하기 때문에 `mysqlclient`가 필요합니다. 그렇기 때문에 `mysqlclient`를 설치합니다.

```shell
pip install mysqlclient
```

![image](https://user-images.githubusercontent.com/78892113/169578433-7a14fe98-b241-4490-a602-38008392ae60.png){: .align-center}{: width="100%" height="100%"}

설치한 후 Airflow를 다시 실행하니 정상적으로 작동합니다.