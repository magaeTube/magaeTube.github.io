---
title:  "[Airflow] 데몬(Daemon) 실행이 되지 않을 때"
date:   2021-01-05 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - DataEngineering
  - Airflow
  - Daemon
  - Error
header-img: https://user-images.githubusercontent.com/78892113/169574664-4efcf70e-f846-4919-9a00-730e8f799fe6.png

header-mask: true

---

![image](https://user-images.githubusercontent.com/78892113/169574664-4efcf70e-f846-4919-9a00-730e8f799fe6.png){: .align-center}{: width="80%" height="80%"}

<br>

안녕하세요 마개입니다.  
Apache Airflow를 이용하여 배치 스케줄링을 관리하려고 합니다. Docker가 아니라 직접 구축하였는데 이 때 발생한 내용을 공유합니다. 

환경은 **Ubuntu 20.04**, **Python 3.8** 환경에서 Airflow 1.10.13 버전으로 설치하였습니다.

처음에 데몬으로 `Scheduler`와 `Webserver`를 이용하고 종료 후에 다시 실행하려고 하였습니다.

```bash
$ airflow scheduler -D
$ airflow webserver -p 8080 -D
```

위와 같이 명령어를 실행하였는데 동작을 하지 않았습니다. (리눅스 프로세스 확인)

다만 두 개 다 **<span style="color:red">-D</span>**를 빼고 실행하면 실행은 되지만 **<span style="color:red">-D</span>** 명령어가 없으면 `Foreground` 환경에서 진행되기 때문에 계속 켜놔야 하는 단점이 있습니다. `Background`에서 실행하기 위해 원인을 파악해봅니다. 

Airflow 기본 폴더(`$AIRFLOW_HOME`)에서 `airflow-scheduler.err`과 `airflow-webserver.err` 파일을 확인합니다.

![image](https://user-images.githubusercontent.com/78892113/170874004-33ec86f4-268b-4a3f-82c9-05387935dce9.png){: .align-center}{: width="80%" height="80%"}

에러의 내용을 보면

```bash
$ FileExistsError: [Errno 17] File exists: '/home/ksh/airflow/airflow-webserver-monitor.pid'
```

위와 같이 메시지가 나와있는 것을 보실 수 있습니다. 이는 즉 기본 폴더 경로에 `.pid`파일들이 있기 때문에 발생한 에러입니다. Airflow를 실행하면 기본 경로에 각 서비스별 프로세스 ID가 담긴 `.pid`파일들이 생성되고 종료하면 해당 파일들은 삭제됩니다. 그러나 Airflow를 정상적인 방법으로 종료하지 않으면(강제종료) `.pid` 파일들이 삭제되지 않습니다. 그 상태에서 다시 실행하면 위와 같은 에러가 발생하게 됩니다. 

이제 `airflow-scheduler.pid`, `airflow-webserver-monitor.pid` 파일들을 삭제하고 이후 다시 명령어를 실행하면 정상적으로 올라온 것을 확인할 수 있습니다.