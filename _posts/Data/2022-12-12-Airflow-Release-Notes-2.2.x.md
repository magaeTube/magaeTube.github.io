---
title:  "[Airflow] Release Notes - 2.2.x"
date:   2022-12-12 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - Airflow
toc: true
toc_sticky: true
toc_label: "Release Notes"
header-img: https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
2021년 10월 11일에 적용된 Airflow 2.2 버전에서의 변경사항은 무엇이 있었는지 공식 홈페이지를 통해 알아봅니다. 메이저한 변경사항에 대해만 알아보고 버그 수정 부분은 생략합니다. 자세한 것은 공식 홈페이지를 통해 확인합니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

* 참고 URL : <a href="https://airflow.apache.org/docs/apache-airflow/stable/release_notes.html#airflow-2-2-0-2021-10-11">공식 홈페이지</a>

<br>

# 메인 변경사항

<br>

## 1. `worker_log_server_port` 설정이 `logging` 세션으로 이동

`[celery]` 세션에 있던 `worker_log_server_port` 설정이 `[logging]` 세션으로 변경됩니다.

<br><br>

## 2. `pandas` 는 선택적 의존성임

이전에는 `pandas` 가 핵심이라서 `pip install apache-airflow` 로 airflow를 설치할 때 `pandas`가 없다면 자동으로 설치되었습니다..  
만약 Airflow와 연동되는 pandas를 설치하고자 하면 Airflow를 설치할 때 `[pandas]`를 추가로 사용할 수 있습니다.  
예를 들어 Python 3.8과 Airflow 2.1.2 환경이라고 치면 아래와 같이 설치할 수 있습니다.  

```sh
$ pip install -U "apache-airflow[pandas]==2.1.2" \
  --constraint https://raw.githubusercontent.com/apache/airflow/constraints-2.1.2/constraints-3.8.txt"
```

<br><br>

## 3. `none_failed_or_skipped` trigger rules은 중단됩니다.

`TriggerRule.NONE_FAILED_OR_SKIPPED`는 `TriggerRule.NONE_FAILED_MIN_ONE_SUCCESS`로 대체됩니다. 단지 이름만 변경될뿐 기능적인 적은 변경되지 않았습니다. 다음 메이저 버전에서는 삭제됩니다.

<br><br>

## 4. Dummy trigger rule은 더이상 사용되지 않습니다.

`TriggerRule.DUMMY`는 `TriggerRule.ALWAYS`로 대체됩니다. 이 또한 이름만 변경되고 다음 메이저 버전에서는 삭제됩니다.

<br><br>

## 5. DAG 동시성 설정은 이름이 변경됩니다. 

`[core]` 속성 중 `dag_concurrency` 설정은 더 나은 이해를 위해 `[core]` 속성의 `max_active_tasks_per_dag`으로 변경됩니다.  
이는 각 DAG에서 동시적으로 실행되는걸 허용하는 task의 최대수입니다.  

```conf
# Before
[core]
dag_concurrency = 16

# Now
[core]
max_active_tasks_per_dag = 16
```

<br>

설정뿐만 아니라 DAG에서도 변경이 되었습니다.

```python
# Before
dag = DAG(
    dag_id="example_dag",
    start_date=datetime(2021, 1, 1),
    catchup=False,
    concurrency=3,
)

# Now
dag = DAG(
    dag_id="example_dag",
    start_date=datetime(2021, 1, 1),
    catchup=False,
    max_active_tasks=3,
)
```

<br><br>

## 6. Task concurrency 파라미터의 이름이 변경되었습니다.

BashOperator의 `task_concurrency`파라미터의 이름이 `max_active_tis_per_dag`으로 변경되었습니다.

<br>

```python
# Before
with DAG(dag_id="task_concurrency_example"):
    BashOperator(task_id="t1", task_concurrency=2, bash_command="echo Hi")
    
# Now
with DAG(dag_id="task_concurrency_example"):
    BashOperator(task_id="t1", max_active_tis_per_dag=2, bash_command="echo Hi")
```

<br><br>

## 7. `processor_poll_interval` 속성은 `scheduler_idle_sleep_time` 으로 변경되었습니다.

`[scheduler]` 속성의 `processor_poll_interval` 속성은 `[scheduler]` 의 `scheduler_idle_sleep_time` 으로 변경되었습니다.  
이는 `SchedulerJob` 내에서 아무 것도 예약되지 않은 경우 Scheduler 루프 끝에 sleep 시간을 설정하는 것을 의미합니다.  

```conf
# Before
[scheduler]
processor_poll_interval = 16

# Now
[scheduler]
scheduler_idle_sleep_time = 16
```

<br><br>

## 8. `[core] store_dag_code` 는 삭제됩니다.

DAG 직렬화는 Airflow 2 버전 이후로 엄격하게 요구되었지만 웹서버가 Code View를 볼 때 유저가 컨트롤할 수 있게 하였습니다.  
`[core] store_dag_code` 가 `True` 로 설정되었다면, 스케줄러는 DAG 파일의 코드를 DB `dag_code` 테이블에 저장합니다. 그리고 웹서버는 해당 테이블의 정보를 읽습니다. `False` 로 지정하면 웹서버는 DAG 파일로부터 읽습니다. 

<br><br>

## 9. 실행 중인 태스크를 Clear하는 것은 `RESTARTING` 상태가 되는 것입니다.

이전에는 실행중인 태스크를 Clear하면 `SHUTDOWN` 상태가 되고 killed되어 `FAILED` 상태가 되었습니다. <a href="https://github.com/apache/airflow/pull/16681">#16681</a> 이후로는 해당 작업은 `RESTARTING` 상태가 되는 것입니다. `FAILED` 상태로 가지 않고 재시도를 합니다.

<br><br>

## 10. sensor가 타임아웃되었다면 재시도하지 않습니다.

이전에는 `retries` 에 지정된 횟수의 +1 까지 재시도하여 `timeout * (retries + 1)` 가 timeout이었다면 이젠 timeout에 도달하면 바로 실패됩니다.

<br><br>

## 11. Webserver에 DAG refresh 버튼은 제거됩니다.

DAG 파서가 DAG를 싱크하기에 더이상 메뉴얼로 refresh할 필요 없습니다.  
마찬가지로 `/refresh`, `/refresh_all` 엔드포인트도 제거됩니다.

<br><br>

## 12. TaskInstance와 TaskReschedule은 `execution_date` 대신 `run_id`로 정의합니다.

<br><br>

## 13. `max_queued_runs_per_dag` 속성은 삭제됩니다.

<br><br>

## 14. Smart sensor는 더이상 사용되지 않습니다.

2.4.0 버전부터는 삭제됩니다.