---
title:  "[Airflow] Release Notes - 2.7.x"
date:   2024-09-29 00:00:00 +0900
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
2023년 08월 18일에 적용된 Airflow 2.7 버전에서의 변경사항은 무엇이 있었는지 공식 홈페이지를 통해 알아봅니다. 메이저한 변경사항에 대해만 알아보고 버그 수정 부분은 생략합니다. 자세한 것은 공식 홈페이지를 통해 확인합니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

* 참고 URL : <a href="https://airflow.apache.org/docs/apache-airflow/stable/release_notes.html#airflow-2-7-0-2023-08-18">공식 홈페이지</a>

<br>

## 메인 변경사항
### 1. Python 3.7 지원 종료 (2.7.0)
* Python 3.7 버전 지원을 종료합니다. 그렇기에 3.8 이상의 버전을 이용해야 합니다.

<br>

### 2. Old Graph View 제거 (2.7.0)
* 이전의 Graph View를 제거하고 새로운 Graph View를 지원합니다.

<br>

### 3. DAG에 아무 파라미터도 정의되지 않는다면 trigger UI은 생략됩니다. (2.7.0)
* 이전 방식을 이용하려면 `show_trigger_form_if_no_params`를 설정해야 합니다.

<br>

### 4. "db init", "db upgrade" 명령어와 "[database] load_default_connections" 속성은 사용중지됩니다. (2.7.0)
* 데이터베이스를 초기화하거나 업그레이드할 때 `airflow db migrate` 명령어를 이용하세요. 기본 connection은 생성하지 않기 때문에 생성하려면 `airflow db migrate` 명령어를 실행한 이후에 `airflow connections create-default-connections` 명령어를 이용해야 합니다.

<br>

### 5. SMTP SSL connection의 경우 "default" 값을 사용합니다. (2.7.0)
* 기존에 "none" 값 대신 "default" 값으로 Python의 `default_ssl_contest`를 사용합니다. 

<br>

### 6. UI, API, CLI에서 connection 테스트를 허용하지 않습니다. (2.7.0)
* 보안상의 이유로 기능적인 테스트는 불가합니다. `core` 섹션의 `test_connection` 속성을 통해 변경가능하고 환경 변수 `AIRFLOW__CORE__TEST_CONNECTION`을 이용해서도 변경 가능합니다.
* 값은 `Disabled`, `Enabled`, `Hidden`을 사용 가능합니다.
* **Disabled** : 기능 테스트와 UI에서 "Test Connection" 버튼을 사용할 수 없습니다.
* **Enabled** : 기능 테스트와 UI에서 "Test Connection" 버튼을 사용 가능합니다.
* **Hidden** : 기능 테스트를 할 수 없고 UI에서 "Test Connection" 버튼을 숨깁니다.

<br>

### 7. 기본 Celery 애플리케이션명이 변경됩니다. (2.7.0)
* `airflow.executors.celery_executor`에서 `airflow.providers.celery.executors.celery_executor`로 이름이 변경됩니다.
* 신규 이름을 사용하기 위해서 설정과 Health check 명령어를 변경해야 합니다.
  * 설정 : `celery` 섹션의 `celery_app_name` 값을 `airflow.providers.celery.executors.celery_executor` 값으로 설정
  * Health Check 명령어 : `airflow.providers.celery.executors.celery_executor.app` 사용.

<br>

### 8. "scheduler.max_tis_per_query"의 디폴트 값이 512에서 16으로 변경되었습니다. (2.7.0)
* Scheduler를 좀더 반응형으로 사용하기 위해 변경되었습니다.

<br>

### 9. 몇몇 Executor는 provider로 변경되었습니다. (2.7.0)
* Celery Executor : "apache-airflow-providers-celery" (>=3.3.0)
* Kubernetes Executor : "apache-airflow-providers-cncf-kubernetes" (>=7.4.0)
* Dask Executor : "apache-airflow-providers-daskexecutor"

<br>

### 10. CronTriggerTimetable이 실행을 생략하고자할 때 더 좋습니다. (2.7.1)
* "catchup=False"가 설정되어 있을 때, 만약 스케줄러가 timetable을 조회하지 않는다면, CronTriggerTimetable은 더 이상 실행을 생략하지 않습니다.

<br>

### 11. "conf.set()"은 "conf.get()"과 동일하게 대소문자를 구분하지 않습니다. (2.7.1)
* conf.set()과 conf.get()은 string 형태로 파라미터를 쓰지 않으면 에러가 발생합니다.


<br><br>

## 기능 개선

### 1. PostgreSQL에서 taskinstance 테이블에 인덱스 추가 (2.7.0)
* 인덱스 추가

