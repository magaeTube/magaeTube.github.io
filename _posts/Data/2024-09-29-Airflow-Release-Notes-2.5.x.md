---
title:  "[Airflow] Release Notes - 2.5.x"
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
2022년 12월 02일에 적용된 Airflow 2.5 버전에서의 변경사항은 무엇이 있었는지 공식 홈페이지를 통해 알아봅니다. 메이저한 변경사항에 대해만 알아보고 버그 수정 부분은 생략합니다. 자세한 것은 공식 홈페이지를 통해 확인합니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

* 참고 URL : <a href="https://airflow.apache.org/docs/apache-airflow/stable/release_notes.html#airflow-2-5-0-2022-12-02">공식 홈페이지</a>

<br>

## 메인 변경사항

<br>

### 1. "airflow dags test"는 더이상 백필 작업을 하지 않는다. (2.5.0)
* `airflow dags backfill`을 이용해서 백필을 할 수 있습니다.

<br>

### 2. "kubernetes" 속성 세션은 "kubernetes_executor"로 이름이 변경되었습니다. (2.5.0)
* "KubernetesPodOperator"는 더이상 핵심 Kubernetes 속성 파라미터를 따라가지 않기 때문에 이 속성은 Kubernetes executor에만 적용되기에 이름을 변경합니다.

<br>

### 3. ExternalTaskSensor 작업이 실패할 때 AirflowException이 발생합니다. (2.5.0)
* Timeout외에 이 오류를 처리하는 모든 코드는 AirflowSensorTimeout뿐만 아니라 AirflowException으로 이동해야 합니다.

<br>

### 4. "scheduler.deactive_stale_dags_interval" 속성은 "scheduler.parsing_cleanup_interval"로 이름을 변경합니다. (2.5.0)
* Airflow3에는 삭제될 예정

<br>

### 5. 환경 변수를 통해 "monkeypatching"을 트리거합니다. (2.5.1)
* 

<br>

### 6. API 파라미터 또는 Params로 넘기는 date-time 필드는 RFC3339를 준수해야 합니다. (2.5.2)
* 기존 API 호출에서 date-time 필드에 인코딩이 안된 "+"을 넘길 수 있었는데 이제 일부 파라미터는 URL 인코딩이 되어야 합니다. (`%2B` 같은)

<br>

### 7. "[webserver] expose_hostname"의 기본값이 False로 변경됩니다. (2.5.2)
* 기존의 True였는데 False로 변경되었습니다. 관리자가 웹 서버 호스트 이름을 사용자에게 공개하도록 해야 합니다.
