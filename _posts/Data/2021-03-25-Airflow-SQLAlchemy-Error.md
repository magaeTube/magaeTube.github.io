---
title:  "[Airflow] Airflow 실행 명령어 에러"
date:   2021-03-25 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - DataEngineering
  - Airflow
header-img: https://user-images.githubusercontent.com/78892113/169574664-4efcf70e-f846-4919-9a00-730e8f799fe6.png

header-mask: true

---

![image](https://user-images.githubusercontent.com/78892113/169574664-4efcf70e-f846-4919-9a00-730e8f799fe6.png){: .align-center}{: width="80%" height="80%"}

<br>

&nbsp;안녕하세요 마개입니다. Apache Airflow를 이용하여 파이프라인을 구축하고 스케줄링을 진행하고 있습니다. 로컬 환경에서 venv를 이용하여 가상환경을 구축하고 Airflow를 설치하였습니다. 설치 후 이를 실행할 때 발생한 에러를 확인해봅니다.
{: .notice--info}

Docker를 이용하지 않고 직접 설치를 하고 이용할 때에는 Airflow를 실행하는 명령어를 날려줘야 합니다.   
(Webserver, Scheduler, Worker 등등 모두 다 따로)

그럴 때 아래와 같은 에러가 발생하였습니다.

```python
ModuleNotFoundError: No module named 'sqlalchemy.ext.declarative.clsregistry'
```

![image](https://user-images.githubusercontent.com/78892113/169700589-ae791be7-786d-47a6-a3fd-6c1273eae08c.png)

에러 메시지를 보면 `sqlalchemy` 모듈을 못 찾는다고 나와있습니다. 하지만 `pip list`를 이용해서 확인해보면 이미 `sqlalchemy`가 설치되어 있는 모습을 볼 수 있습니다. 

원인을 찾아보니 Airflow의 버전에 비해 `sqlalchemy` 버전이 높았던 것입니다.

기존에 설치되어 있던 버전들을 확인하니 다음과 같습니다.
* `apache-airflow` : 1.10.13
* `SQLAlchemy` : 1.4.2

위와 같은데 SQLAlchemy 버전을 1.3.15 버전으로 다운그레이드하였더니 위와 같은 문제가 해결이 되었습니다.