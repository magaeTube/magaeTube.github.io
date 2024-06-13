---
title:  "[Airflow] DAG에 이슈가 있을 때 체크포인트"
date:   2023-01-28 00:00:00 +0900
categories:
  - Data
  - Airflow
tags:
  - Data
  - Airflow
toc: true
toc_sticky: true
toc_label: "Medikit"
header-img: https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
Airflow를 운영함에 있어 DAG에 이슈가 있을 때 체크해봐야할 사항들에 대해 알아봅니다. 자료는 Marc Lamberti님의 자료를 참고했습니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# DAG가 UI에 나타나지 않을 때 

* UI에 import error가 있는지 체크합니다. error가 발견되면 코드 상에 error가 있는 것입니다.
* 페이지를 새로고침하기 전에 5분 기다리세요.
* DAG를 볼 수 있는지 Roles에서 권한을 확인하세요.
* **`airflow dags list`** CLI 명령어를 통해 DAG가 등록되었는지 확인하세요.
* Webserver와 Scheduler를 재시작해보세요.
* DAGs 폴더에 DAG 파일이 있는지 체크하세요.

<br><br>

# 의존성 충돌이 있을 때 

* **`PythonVirtualenvOperator`**를 사용하세요.
* **`DockerOperator`**를 사용하세요.
* **`KubernetesPodOperator`**를 사용하세요.

<br><br>

# Task가 실행되지 않을 때 (Running)

* DAG가 중지 상태가 아닌지 확인하세요.
* DAG의 **`start_date`**가 과거 날짜인지 확인하세요.
* Scheduler가 정상적으로 실행 중인지 확인하고 아니라면 재시작하세요.
* 기본 pools 또는 그외에 Worker의 여유 slot이 충분한지 확인하세요.
* DAG 단계의 **`concurrency`**와 **`max_active_tasks`** 속성을 확인하세요.
* Task 단계의 **`max_active_tis_per_dag`**과 **`task_concurrency`**를 확인하세요.
* Airflow 차원에서 **`parallelism`**과 **`max_active_runs_per_dag`**를 확인하세요.

<br><br>

# Log가 보이지 않을 때 

* Task Instance를 clear하여 task를 재실행해보세요.
* **`log_fetch_timeout_sec`** 속성을 5초 이상으로 늘려보세요.
* Worker의 가용 자원을 늘려보세요. (CeleryExecutor일 때)
* **`delete_kubernetes_worker_pods`** 속성을 **`False`**로 설정하세요. (K8sExecutor일 때)