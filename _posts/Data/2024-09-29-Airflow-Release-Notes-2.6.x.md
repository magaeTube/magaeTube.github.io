---
title:  "[Airflow] Release Notes - 2.6.x"
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
2022년 12월 02일에 적용된 Airflow 2.6 버전에서의 변경사항은 무엇이 있었는지 공식 홈페이지를 통해 알아봅니다. 메이저한 변경사항에 대해만 알아보고 버그 수정 부분은 생략합니다. 자세한 것은 공식 홈페이지를 통해 확인합니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

* 참고 URL : <a href="https://airflow.apache.org/docs/apache-airflow/stable/release_notes.html#airflow-2-6-0-2023-04-30">공식 홈페이지</a>

<br>

## 메인 변경사항

<br>

### 1. file task 로그 디렉토리에 대한 디폴트 권한이 "owner + group"가 수정가능하도록 변경됩니다. (2.6.0)
* 기존 디폴트 값은 다른 사람들도 수정할 수 있었고 유저는 선택해야 했습니다.

<br>

### 2. SLA 콜백은 더 이상 DAG 프로세서 매니저의 큐에 파일을 추가하지 않습니다. (2.6.0)
* DAG processor 매니저가 바쁜 것을 방지하기 위해 SLA 콜백을 이용하지 않습니다.

<br>

### 3. "scheduler.tasks.running" 게이지는 더 이상 존재하지 않습니다. (2.6.0)
* 이 게이지는 작동한적이 없고 항상 값이 0이었습니다. 이 메트릭의 정확한 값을 갖는 것은 복잡했기에 제거했습니다.

<br>

### 4. run_id로 사용할 수 있는 패턴이 `^[A-Za-z0-9_.~:+-]+$`로 변경되었습니다. (2.6.3)
* 기존에는 `run_id`에 대한 특별한 validation이 없었는데 `scheduler` 섹션에 `allowed_run_id_pattern`을 이용하여 validation 을 구성할 수 있습니다.

<br><br>

## 기능 개선

### 1. 실행중인 속성은 오직 속성 뷰에서만 볼 수 있습니다. (2.6.0)
* 이전에는 기본 속성이 상단에 표기되었지만 기본 속성이 오버라이드되는지 아닌지 확실치 않았습니다.

<br><br>

## 기타 개선
### 1. next_run_calculation 백오프 시 OverflowError를 처리합니다. (2.6.0)
* 최대 Task 재시도 지연 값은 기본 값이 24시간 (86,400초)로 설정되어 있습니다. `core.max_task_retry_delay` 파라미터를 이용해서 변경할 수 있습니다.

<br>

### 2. Hive 매크로를 provider로 이동 (2.6.0)
* Hive Provider가 설치되어 있을 때만 Hive 매크로 (`hive.max_partition`, `hive.closest_ds_partition`)을 이용할 수 있습니다.

<br>

### 3. FIPS v2에 대한 캐싱 해시 방법을 지원하도록 앱이 업데이트되었습니다. (2.6.0)
* Python 3.9 이상으로 실행되는 Airflow를 사용할 때 FIPS를 준수할 수 있도록 업데이트되었습니다. 이는 신규 옵션인 `caching_hash_method`를 지원합니다.
