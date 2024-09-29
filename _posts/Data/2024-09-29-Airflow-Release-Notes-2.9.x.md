---
title:  "[Airflow] Release Notes - 2.9.x"
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
2024년 04월 08일에 적용된 Airflow 2.9 버전에서의 변경사항은 무엇이 있었는지 공식 홈페이지를 통해 알아봅니다. 메이저한 변경사항에 대해만 알아보고 버그 수정 부분은 생략합니다. 자세한 것은 공식 홈페이지를 통해 확인합니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

* 참고 URL : <a href="https://airflow.apache.org/docs/apache-airflow/stable/release_notes.html#airflow-2-9-0-2024-04-08">공식 홈페이지</a>

<br>

## 메인 변경사항
### 1. 다음 Listener API 메소드는 안정된 메소드로 운영에 사용할 수 있습니다. (2.9.0)
* Lifecycle events
  * on_starting
  * before_stopping
* DagRun State Change Events
  * on_dag_run_running
  * on_dag_run_success
  * on_dag_run_failed
* TaskInstance State Change Events
  * on_Task_instance_running
  * on_task_instance_success
  * on_task_instance_failed

<br>

### 2. Airflow Meta Database에 MS-SQL은 제외되었습니다. (2.9.0)
* Airflow의 PMC 멤버와 커미터들끼리의 회의 결과로 MS-SQL 유지보수를 그만하기로 함.

<br>

### 3. Dataset URI를 input 으로 사용할 수 있습니다. (2.9.0)
* Dataset은 AIP-60에 명시된 규칙을 준수하는 URI를 사용해야 하며 DAG 파일이 구문 분석될 때 자동으로 정규화됩니다.

<br>

### 4. Python 3.12 버전을 지원합니다. (2.9.0)
* Python 3.12에서 Pendulum 2는 지원하지 않기 때문에 3 버전을 이용해야 합니다.

<br>

### 5. Rendered Template 필드에 저장되는 String 객체의 길이를 제한함. (2.9.0)
* 기본 값은 4096 캐릭터로 제한됩니다. `[core] max_template_field_length` 속성을 통해 변경할 수 있습니다.

<br>

### 6. xcom 테이블의 value 컬럼의 타입이 longblob으로 변경되었습니다. (2.9.0)
* `value` 컬럼의 타입이 blob에서 longblob으로 변경되었습니다. 이는 Xcom에 더 큰 데이터를 저장할 수 있습니다.
