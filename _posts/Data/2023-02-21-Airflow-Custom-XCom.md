---
title:  "[Airflow] 커스텀 XCom 사용"
date:   2023-02-21 00:00:00 +0900
categories:
  - Data
  - Airflow
tags:
  - Data
  - Airflow
toc: true
toc_sticky: true
toc_label: "XCom"
header-img: https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
Airflow를 이용하는데 있어 Task 간에 데이터를 주고 받는 방법에는 여러 방법이 있는데 기본적으로는 `XCom`이라는 기능을 제공합니다. 이 XCom에 대해서 Custom해보는 과정을 알아봅니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# 기본 XCom의 한계 
&nbsp;Airflow에는 Task 간의 데이터 전달을 위해 `XCom`이라는 기능을 제공하는데 이 XCom은 `Metadata Database`에 저장하고 가져오는 방식이기 때문에 데이터를 저장하는데 한계가 있습니다. 각 Database 종류에 따라 허용되는 사이즈는 아래와 같습니다.

<br>

| DB       | Size  |
|----------|-------|
| SQLite   | 2 GB  |
| Postgres | 1 GB  |
| MySQL    | 64 KB |

<br>

&nbsp;각 XCom 데이터는 SQLAlchemy가 제공하는 `LargeBinary` 타입의 컬럼에 저장되는데 이는 MySQL에서는 `BLOB` 타입, PostgreSQL에서는 `BYTEA` 타입에 해당되기 때문에 용량도 각각 다르게 됩니다.  
&nbsp;이러한 데이터 저장 용량의 한계 때문에 `Custom XCom Backend`를 개발하였습니다. Custom XCom에서는 각 클라우드의 스토리지를 이용할 수 있는데 AWS의 `S3`를 이용해서 저장 용량의 한계가 없도록 진행했습니다.  
&nbsp;Custom XCom Backend를 만들기 위해서는 `BaseXCom`을 상속받아 진행해야 합니다.

<br><br>

# XCom 메커니즘
&nbsp;진행하기 전에 앞서 XCom을 이용해서 데이터를 주고 받을 때는 `직렬화`와 `역직렬화`의 과정을 거칩니다. 둘다 BaseXCom 클래스를 이용하는데 데이터를 저장(push)할 때는 `serialize_value` 메서드를 이용하고 가져올(pull) 때는 `deserialize_value` 메서드를 이용합니다.  
&nbsp;Custom XCom을 작성할 때에는 이 부분을 오버라이딩해서 Metadata Database가 아닌 원하는 `Storage`(local, AWS, GCP, Azure)에 저장하는 로직으로 변경하면 됩니다.

<br><br>

# Custom XCom 코드 작성
## 디렉터리 구조

![image](https://user-images.githubusercontent.com/78892113/221406006-de17f765-ddcb-4414-b322-7c27ee3dfc0e.png)

&nbsp;Custom으로 제작하는 소스들의 경우 전부 `plugins` 디렉터리에 넣어둡니다. `plugins` 안에는 `custom`, `include`가 있는데 `custom`에는 custom operator와 hook이 있고 `include` 디렉터리 안에 Custom XCom Backend를 작성했습니다. (파일명 : aws_xcom_backend.py)

<br>

## 코드

```python
# include/aws_xcom_backend.py
from airflow.models.xcom import BaseXCom
from airflow.providers.amazon.aws.hooks.s3 import S3Hook

from typing import Any
import common.constant.Global as const
import pandas as pd
import uuid
import os
import json


class CustomXComBackendS3(BaseXCom):
    PREFIX = "xcom_s3://"
    BUCKET_NAME = "airflow_xcom_test"
    AWS_CONN_ID = "aws_conn_default"

    @staticmethod
    def serialize_value(
        value: Any,
        key: str | None = None,
        task_id: str | None = None,
        dag_id: str | None = None,
        run_id: str | None = None,
        map_index: str | None = None,
        **kwargs
    ):

        hook = S3Hook(aws_conn_id=CustomXComBackendS3.AWS_CONN_ID)

        if isinstance(value, pd.DataFrame):
            filename = f"data_{str(uuid.uuid4())}.parquet"
            s3_key = f"temp/airflow/{dag_id}/{run_id}/{task_id}/{filename}"

            value.to_parquet(filename, compression="gzip")
        else:
            filename = f"data_{str(uuid.uuid4())}.json"
            s3_key = f"temp/airflow/{dag_id}/{run_id}/{task_id}/{filename}"

            with open(filename, "a+") as f:
                json.dump(value, f)

        hook.load_file(
            filename=filename,
            key=s3_key,
            bucket_name=CustomXComBackendS3.BUCKET_NAME,
            replace=True
        )

        os.remove(filename)

        reference_string = CustomXComBackendS3.PREFIX + s3_key

        return BaseXCom.serialize_value(value=reference_string)

    @staticmethod
    def deserialize_value(result):
        result = BaseXCom.deserialize_value(result)

        hook = S3Hook(aws_conn_id=CustomXComBackendS3.AWS_CONN_ID)
        key = result.replace(CustomXComBackendS3.PREFIX, "")

        filename = hook.download_file(
            key=key,
            bucket_name=CustomXComBackendS3.BUCKET_NAME,
            local_path="/tmp"
        )

        if key.split(".")[-1] == "parquet":
            output = pd.read_parquet(filename)
        else:
            with open(filename, "r") as f:
                output = json.load(f)

        os.remove(filename)

        return output
```

&nbsp;코드 자체는 간단합니다. `xcom_push` 를 통해 데이터를 저장할 때는 `serialize_value`를 이용하고 `xcom_pull`로 데이터를 불러올 때는 `deserialize_value`를 이용하게 됩니다. 그래서 이 두 부분을 변경하면 됩니다.  
&nbsp;XCom으로 데이터를 주고받을 때 사용하는 오브젝트는 여러 가지가 있지만 Pandas의 DataFrame을 이용하면 `parquet`형태로 저장하고 그 외에는 `json` 형태로 저장한 후 S3Hook을 이용해서 데이터를 load하고 download하는 방식입니다.

<br>

## 설정 변경

위의 코드를 개발했다면 이를 Airflow설정에 지정하여 기본 XCom으로 사용되도록 해야 합니다. 

```conf
# airflow.cfg
[core]
xcom_backend=include.aws_xcom_backend.CustomXComBackendS3

# 환경변수
AIRFLOW__CORE__XCOM_BACKEND=include.aws_xcom_backend.CustomXComBackendS3
```

설정에는 위와 같은 방법으로 지정하면 되는데 `xcom_backend`, `AIRFLOW__CORE__XCOM_BACKEND`에 우리가 위에서 만들었던 패키지명을 지정하면 됩니다.

<br>

## 재시작
설정이 완료되면 Airflow를 재시작합니다. 재시작하고 적용이 잘 되었는지는 파이썬 코드를 이용해서 확인합니다.

<br>

```python
from airflow.models.xcom import XCom

print(XCom.__name__)
```

![image](https://user-images.githubusercontent.com/78892113/221406680-11194e66-12f1-454d-9491-d6ab51095d2f.png)

위와 같이 제대로 나온다면 적용 완료되었습니다.

<br>

## 사용

사용하는 방법은 특별하게 다른게 아니라 기존에 사용하던 XCom 사용 방법으로 하면 됩니다.

<br>

## 데이터 확인

XCom을 사용하면 데이터는 S3에서 확인할 수 있습니다.
* **버킷명** : airflow_xcom_test
* **Key** : temp/airflow/[DAG ID]/[Run ID]/[Task ID]/data_해시값
* **파일 확장자** : `.parquet` 또는 `.json`

