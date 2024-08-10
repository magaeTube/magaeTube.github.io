---
title:  "[Airflow] Release Notes - 2.3.x"
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
2022년 04월 30일에 적용된 Airflow 2.3 버전에서의 변경사항은 무엇이 있었는지 공식 홈페이지를 통해 알아봅니다. 메이저한 변경사항에 대해만 알아보고 버그 수정 부분은 생략합니다. 자세한 것은 공식 홈페이지를 통해 확인합니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

* 참고 URL : <a href="https://airflow.apache.org/docs/apache-airflow/stable/release_notes.html#airflow-2-3-0-2022-04-30">공식 홈페이지</a>

<br>

## 메인 변경사항

<br>

### 1. `execution_date`를 XCom.set() , XCom.clear() , XCom.get_one() , XCom.get_many() 로 넘기는 것은 더 이상 사용되지 않습니다.

대신 `run_id`를 이용하세요.

<br><br>

### 2. 태스크의 로그 템플릿은 이제 `airflow.cfg` 가 아니라 메타데이터 데이터베이스에서 읽습니다.

이전에는 `[core]` 세션의 `log_filename_template` , `[elasticsearch]` 세션의 `log_id_template` 속성을 이용해서 지정했었는데 Airflow 인스턴스가 실행된 후 값을 수정하면 기존의 로그들은 이전 형식으로 저장되고 새로 지정한 구성으로 되지 못하는 문제가 발생합니다.  
이에 따라 `log_template` 라는 테이블을 만들어서 해당 문제를 해결하였습니다. 이 테이블은 Airflow가 시작되는 매시간 이전 속성값과 동기화됩니다. 새로운 필드 `log_template_id` 가 추가되었습니다. 이 필드는 모든 DAG run에 추가되어 태스크들에 사용되는 포맷을 지정합니다.

<br><br>

### 3. 쿠버네티스 라이브러리의 최소 버전이 `3.0.0` 에서 `21.7.0` 으로 증가되었습니다.

이는 단지 `쿠버네티스` 라이브러리에 대한 변경일뿐 쿠버네티스 클러스터에 관한 것이 아닙니다. Airflow가 지원하는 쿠버네티스 버전은 <a href="https://airflow.apache.org/docs/apache-airflow/stable/installation/prerequisites.html">여기</a>에서 확인 가능합니다.

<br><br>

### 4. XCom에서 `execution_date` 대신 `run_id`를 이용합니다.

XCom에서 `execution_date` 컬럼은 삭제되었습니다. 대신 `run_id` 컬럼을 이용하면 됩니다.

<br><br>

### 5. JSON 직렬화되지 않는 `params`는 더 이상 사용되지 않습니다.

이전에는 `dag`나 `task`에서 `param` 파라미터로 JSON 직렬화를 하지 않아도 사용 가능했습니다.  
아래와 같은 코드는 동작이 되었던 것이죠.

<br>

```python
@dag.task(params={"a": {1, 2, 3}, "b": pendulum.now()})
def datetime_param(value):
    print(value)


datetime_param("{{ params.a }} | {{ params.b }}")
```

`set`와 `datetime`타입은 JSON 직렬화가 아닙니다. 이는 dag run의 기본 설정을 오버라이드하기 때문에 문제가 될 수 있습니다. 그렇기때문에 설정을 overwrite하지 않기 위해 JSON으로 직렬화해야 합니다.

<br><br>

### 6. SQLAlchemy가 1.4.0 버전 이상으로 업그레이드됨에 따라 `sql_alchemy_conn` 속성에서 `postgres://` 대신 `postgresql://` 을 이용해야 합니다.

SQLAlchemy 1.4.0 이전 버전에서는 `sql_alchemy_conn` 속성에 `postgres://` 사용이 가능했지만 1.4.0 이상의 버전에서는 `postgresql://` 로 사용해야 합니다.  
1.4.0 이상의 버전에서 `postgres://`을 사용하면 아래와 같은 에러가 발생합니다.

```python
>       raise exc.NoSuchModuleError(
            "Can't load plugin: %s:%s" % (self.group, name)
        )
E       sqlalchemy.exc.NoSuchModuleError: Can't load plugin: sqlalchemy.dialects:postgres
```

만약에 URL을 당장 바꾸기가 어렵다면 SQLAlchemy를 1.3 버전으로 다운그레이드해서 이용하세요.

<br><br>

### 7. `auth_backend` 속성이 `auth_backends` 로 변경되었습니다.

기존에는 하나의 backend만 REST API을 사용할 수 있었는데 2.3 이후에 , 로 구분하여 여러 backends를 지원하게 되었습니다.

<br><br>

### 8. `airflow.models.base.Operator` 는 삭제되었습니다.

타입 힌트를 위해 사용되었던 빈 클래스인 `airflow.models.base.Operator` 가 유용하지 않아서 삭제되었습니다. 대신 `airflow.models.baseoperator.BaseOperator` 를 사용하세요.

<br><br>

### 9. `auth_backends` 에 세션이 포함되었습니다.

API를 사용하기 위해서 UI를 허용하기 위해 이전에는 `airflow.api.auth.backend.deny_all`이 `airflow.api.auth.backend.session`으로 변경되었습니다. 그리고 이것은 기본값이 설정되어 있지 않다면 API가 허가된 backend들의 리스트가 추가됩니다.

<br><br>

### 10. `Connection.extra`는 JSON 형태의 dict로 작성해야 합니다.

Airflow Connection에 들어가는 정보 중에 `extra`라는 필드가 있습니다. 해당 필드에 데이터를 넣을 때는 `dict`형태로 넣어줘야 합니다.

<br><br>

### 11. 기본 view 설정값인 `tree`가 `grid`로 이름이 변경되었습니다.

<br><br>

### 12. 데이터베이스 속성이 새로운 세션으로 이동되었습니다.

데이터베이스 관련된 속성들이 기존에 `[core]` 세션에서 새로운 세션인 `[database]` 세션으로 이동되었습니다.

* sql_alchemy_conn
* sql_engine_encoding
* sql_engine_collation_for_ids
* sql_alchemy_pool_enabled
* sql_alchemy_pool_size
* sql_alchemy_max_overflow
* sql_alchemy_pool_recycle
* sql_alchemy_pool_pre_ping
* sql_alchemy_schema
* sql_alchemy_connect_args
* load_default_connections
* max_db_retries

<br><br>

### 13. Flask App Builder를 4.* 버전으로 업그레이드 했습니다.

