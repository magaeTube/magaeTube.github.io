---
title:  "[Airflow] Release Notes - 2.4.x"
date:   2022-12-12 00:00:00 +0900
categories:
  - Data
  - Airflow
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
2022년 09월 19일에 적용된 Airflow 2.4 버전에서의 변경사항은 무엇이 있었는지 공식 홈페이지를 통해 알아봅니다. 메이저한 변경사항에 대해만 알아보고 버그 수정 부분은 생략합니다. 자세한 것은 공식 홈페이지를 통해 확인합니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

* 참고 URL : <a href="https://airflow.apache.org/docs/apache-airflow/stable/release_notes.html#airflow-2-4-0-2022-09-19">공식 홈페이지</a>

<br>

# 메인 변경사항

<br>

## 1. `Dataset` 이라는 새로운 개념과 `Data-aware` 스케줄링이 추가되었습니다.

스케줄링 방법 중에서 `dataset`이 업데이트되면 실행되는 방법입니다.  
* 참고 <a href="https://airflow.apache.org/docs/apache-airflow/stable/concepts/datasets.html#">URL</a>

<br><br>

## 2. context manager에서 사용되는 DAG들은 더이상 변수로 할당하지 않아도 됩니다.

```python
# AS-IS
with DAG(dag_id="example") as dag:
    ...


@dag
def dag_maker():
    ...


dag2 = dag_maker()


# TO-BE
with DAG(dag_id="example"):
    ...


@dag
def dag_maker():
    ...


dag_maker()
```

만약에 위와 같이 자동으로 지정되는 것이 싫다면 `auto_register=False` 를 지정하면 됩니다.

<br>

```python
# This dag will not be picked up by Airflow as it's not assigned to a variable
with DAG(dag_id="example", auto_register=False):
    ...
```

<br><br>

## 3. `schedule_interval` 과 `timetable` argument는 더이상 사용되지 않습니다.

대신 `schedule` 이라는 argument를 추가하여 cron 표현식이나 timedelta 객체, timetable 객체 또는 dataset 객체들을 수용할 수 있습니다. 

```python
# AS-IS
with DAG(
    dag_id="my_example",
    start_date=datetime(2021, 1, 1),
    schedule_interval="@daily",
):
    ...
    
with DAG(
    dag_id="my_example",
    start_date=datetime(2021, 1, 1),
    timetable=EventsTimetable(event_dates=[pendulum.datetime(2022, 4, 5)]),
):
    ...

    
# TO-BE
with DAG(
    dag_id="my_example",
    start_date=datetime(2021, 1, 1),
    schedule="@daily",
):
    ...
    
with DAG(
    dag_id="my_example",
    start_date=datetime(2021, 1, 1),
    schedule=EventsTimetable(event_dates=[pendulum.datetime(2022, 4, 5)]),
):
    ...
```

<br><br>

## 4. Smart Sensor는 삭제되었습니다.

<br><br>

## 5. `DBApiHook`과 `SQLSensor`는 `apache-airflow-providers-common-sql` provider로 이동했습니다.

<br><br>

## 6. 설정에 `[webserver]` 의 `expose_stacktrace` 기본값이 `True`에서 `False` 로 변경되었습니다.